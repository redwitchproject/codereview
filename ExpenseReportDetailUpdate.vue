<template>
  <div>
    <transition
      name="fade"
      mode="out-in"
      enter-active-class="transform ease-out duration-300 transition"
      enter-class="translate-y-2 opacity-0 sm:translate-y-0 sm:translate-x-2"
      enter-to-class="translate-y-0 opacity-100 sm:translate-x-0"
      leave-active-class="transition ease-in duration-100"
      leave-class="opacity-100"
      leave-to-class="opacity-0"
    >
      <div
        v-if="showAlert"
        class="z-10 fixed inset-0 flex items-end justify-center px-4 py-6 pointer-events-none sm:p-6 sm:items-start sm:justify-end"
      >
        <div
          class="max-w-sm w-full bg-white shadow-lg rounded-lg pointer-events-auto"
        >
          <div class="rounded-lg shadow-xs overflow-hidden">
            <div class="p-4">
              <div class="flex items-start">
                <div class="flex-shrink-0">
                  <svg
                    class="h-6 w-6 text-green-400"
                    fill="none"
                    viewBox="0 0 24 24"
                    stroke="currentColor"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width="2"
                      d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"
                    />
                  </svg>
                </div>
                <div class="ml-3 w-0 flex-1 pt-0.5">
                  <p class="text-sm leading-5 font-medium text-gray-900">
                    {{ alertText }}
                  </p>
                </div>
                <div class="ml-4 flex-shrink-0 flex">
                  <button
                    @click="showAlert = false"
                    class="inline-flex text-gray-400 focus:outline-none focus:text-gray-500 transition ease-in-out duration-150"
                  >
                    <svg
                      class="h-5 w-5"
                      viewBox="0 0 20 20"
                      fill="currentColor"
                    >
                      <path
                        fill-rule="evenodd"
                        d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z"
                        clip-rule="evenodd"
                      />
                    </svg>
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </transition>

    <GeneralInfo
      :user="user"
      :report-total-amount="reportTotalAmount"
      :report-total-amount-currency="reportTotalAmountCurrency"
      :status="status"
      :status-display="statusDisplay"
      :report-is-submitted="isReportSubmitted"
      :report-is-draft="isReportDraft"
      :report-is-withdrawn="isReportWithdrawn"
      :report-is-rejected="isReportRejected"
      :report-is-approved="isReportApproved"
      :report-is-paid="isReportPaid"
      :user-is-requester="isUserRequester"
      :user-is-staff="isUserStaff"
      :request-user-currency="requestUserCurrency"
      :expenses="expenses"
      @submitReport="submit($event)"
      @rejectReport="reject($event)"
      @approveReport="approve($event)"
      @withdrawReport="withdraw"
      @deleteReport="deleteReport"
      @converyCurrency="converyCurrency($event)"
      :convertedAmount="convertedAmount"
      :convertedCurrency="convertedCurrency"
      :convertedExpenses="convertedExpenses"
      :is-report-in-request-user-currency="isReportInRequestUserCurrency"
      :rejection-error="rejectionError"
    />
    <Expenses
      :user-is-requester="isUserRequester"
      :user-is-staff="isUserStaff"
      :report-is-submitted="isReportSubmitted"
      :report-is-draft="isReportDraft"
      :report-is-withdrawn="isReportWithdrawn"
      :report-is-rejected="isReportRejected"
      :report-is-approved="isReportApproved"
      :report-is-paid="isReportPaid"
      :expenses="expenses"
      :convertedExpenses="convertedExpenses"
      @deleteExpenseItem="deleteItem($event)"
    />
    <History
      :items="sortedHistory"
      :created="created"
      :request-user-name="requestUserName"
      :user-is-requester="isUserRequester"
      :user-is-staff="isUserStaff"
      :user="user"
      :expense-report-slug="expenseReportSlug"
      :report-total-amount="reportTotalAmount"
      :report-total-amount-currency="reportTotalAmountCurrency"
      :loggedInUser="loggedInUser"
      :report-is-submitted="isReportSubmitted"
      :report-is-draft="isReportDraft"
      :report-is-withdrawn="isReportWithdrawn"
      :report-is-rejected="isReportRejected"
      :report-is-approved="isReportApproved"
      :report-is-paid="isReportPaid"
    />
  </div>
</template>

<script>
import GeneralInfo from "./GeneralInfo";
import Expenses from "./Expenses";
import History from "./History";

export default {
  name: "ExpenseReportDetailUpdate",
  components: {
    GeneralInfo,
    Expenses,
    History
  },
  data: function() {
    return {
      approvedBy: "",
      datePaid: null,
      expenses: [],
      history: [],
      reportTotalAmount: 0,
      reportTotalAmountCurrency: "",
      requestUserCurrency: "",
      requestUserRole: "",
      requestUserName: "",
      status: "",
      statusDisplay: "",
      submitted: "",
      created: "",
      user: {},
      displayAlcoholExpenseConfirmation: false,
      isReportInRequestUserCurrency: true,

      sortedHistory: [],
      loggedInUser: "",
      expenseReportSlug: "",
      showAlert: false,
      convertedAmount: null,
      convertedCurrency: "",
      convertedExpenses: [],
      rejectionError: false
    };
  },
  methods: {
    getCookie: function(name) {
      var match = document.cookie.match(new RegExp(name + "=([^;]+)"));
      if (match) return match[1];
      return;
    },
    getData: function() {
      this.$http
        .get(`/api/expenses/report/${this.expenseReportSlug}/`)
        .then(function(response) {
          this.approvedBy = response.body.approved_by;
          this.datePaid = response.body.date_paid;
          this.expenses = response.body.expenses;
          this.history = response.body.history;
          this.reportTotalAmount = response.body.report_total_amount;
          this.reportTotalAmountCurrency =
            response.body.report_total_amount_currency;
          this.requestUserCurrency = response.body.request_user_currency;
          this.requestUserRole = response.body.request_user_role;
          this.requestUserName = response.body.request_user_name;
          this.status = response.body.status;
          this.statusDisplay = response.body.status_display;
          this.submitted = response.body.submitted;
          this.created = response.body.created;
          this.user = response.body.user;
          this.displayAlcoholExpenseConfirmation =
            response.body.display_alcohol_expense_confirmation;
          this.isReportInRequestUserCurrency =
            response.body.is_report_in_request_user_currency;

          this.sortHistory();
        });
    },
    getLoggedInUser: function() {
      this.$http.get(`/api/user/me/`).then(function(response) {
        this.loggedInUser = response.body.name;
      });
    },
    deleteItem: function(data) {
      this.$http
        .delete(`/api/expense/delete/${data}/`, {
          headers: { "X-CSRFToken": this.getCookie("csrftoken") }
        })
        .then(function() {
          this.alertText = "Expense deleted.";
          this.showAlert = true;
          setTimeout(() => (this.showAlert = false), 5000);
          this.getData();
        })
        .catch(err => {
          console.log(err);
          this.errors = err.body;
          setTimeout(() => (this.errors = []), 5000);
        });
    },
    submit: function(data) {
      this.$http
        .post(
          `/api/report/submit/${this.expenseReportSlug}/`,
          { requester_note: data },
          { headers: { "X-CSRFToken": this.getCookie("csrftoken") } }
        )
        .then(function() {
          this.alertText = "Your expenses have been submitted for approval.";
          this.showAlert = true;
          setTimeout(() => (this.showAlert = false), 5000);
          this.getData();
        })
        .catch(err => {
          console.log(err);
          this.errors = err.body;
          setTimeout(() => (this.errors = []), 5000);
        });
    },
    reject: function(data) {
      this.$http
        .post(
          `/api/report/reject/${this.expenseReportSlug}/`,
          { rejection_note: data },
          { headers: { "X-CSRFToken": this.getCookie("csrftoken") } }
        )
        .then(function() {
          this.alertText =
            "Expense Report rejected. Requester will be notified.";
          this.showAlert = true;
          setTimeout(() => (this.showAlert = false), 5000);
          this.getData();
        })
        .catch(err => {
          console.log(err);
          this.rejectionError = true;
          // setTimeout(() => (this.rejectionError = false), 5000);
        });
    },
    approve: function(data) {
      this.$http
        .post(
          `/api/report/approve/${this.expenseReportSlug}/`,
          { approval_note: data },
          { headers: { "X-CSRFToken": this.getCookie("csrftoken") } }
        )
        .then(function() {
          this.alertText =
            "Expense Report approved. Requester will be notified and payment processed.";
          this.showAlert = true;
          setTimeout(() => (this.showAlert = false), 5000);
          this.getData();
        })
        .catch(err => {
          console.log(err);
          this.errors = err.body;
          setTimeout(() => (this.errors = []), 5000);
        });
    },
    withdraw: function() {
      this.$http
        .post(
          `/api/report/withdraw/${this.expenseReportSlug}/`,
          {},
          { headers: { "X-CSRFToken": this.getCookie("csrftoken") } }
        )
        .then(function() {
          this.alertText = "Your expense report has been withdrawn.";
          this.showAlert = true;
          setTimeout(() => (this.showAlert = false), 5000);
          this.getData();
        })
        .catch(err => {
          console.log(err);
          this.errors = err.body;
          setTimeout(() => (this.errors = []), 5000);
        });
    },
    deleteReport: function() {
      this.$http
        .delete(`/api/report/delete/${this.expenseReportSlug}/`, {
          headers: { "X-CSRFToken": this.getCookie("csrftoken") }
        })
        .then(function() {
          this.alertText = "Expense Report has been deleted.";
          this.showAlert = true;
          setTimeout(() => (this.showAlert = false), 5000);
          window.location = "/expenses/~reports";
        })
        .catch(err => {
          console.log(err);
          this.errors = err.body;
          setTimeout(() => (this.errors = []), 5000);
        });
    },
    converyCurrency: function(convert) {
      if (convert) {
        this.$http
          .get(
            `/api/expenses/report/${this.expenseReportSlug}/?currency=${this.requestUserCurrency}`
          )
          .then(function(response) {
            this.convertedAmount = response.body.report_total_amount;
            this.convertedCurrency = response.body.report_total_amount_currency;
            this.convertedExpenses = response.body.expenses;
          });
      } else {
        this.convertedAmount = null;
        this.convertedCurrency = "";
        this.convertedExpenses = [];
      }
    },
    displayTimelineItem: function(event) {
      if (event.action == "sent_report" && event.user == this.loggedInUser) {
        return true;
      } else if (
        event.action == "sent_report" &&
        event.user != this.loggedInUser
      ) {
        return false;
      } else {
        return true;
      }
    },
    sortHistory() {
      this.sortedHistory = this.history.map(item => {
        item.sortDate = Date.parse(item.created);
        item.displayTimeline = this.displayTimelineItem(item);
        return item;
      });

      this.sortedHistory.sort(function(a, b) {
        if (a.sortDate < b.sortDate) return -1;
        if (a.sortDate > b.sortDate) return 1;
        return 0;
      });
    }
  },
  mounted: function() {
    this.expenseReportSlug = window.expenseReportSlug; // expense report UUID
    this.getLoggedInUser();
    this.getData();
    console.log(this.reportTotalAmountCurrency);
  },
  computed: {
    isUserRequester() {
      return this.requestUserRole == "requester";
    },
    isUserStaff() {
      return (
        this.requestUserRole == "company_admin" ||
        this.requestUserRole == "recruiter" ||
        this.requestUserRole == "approver"
      );
    },
    isReportSubmitted() {
      return this.status == "submitted";
    },
    isReportDraft() {
      return this.status == "draft";
    },
    isReportWithdrawn() {
      return this.status == "withdrawn";
    },
    isReportRejected() {
      return this.status == "rejected";
    },
    isReportApproved() {
      return this.status == "approved";
    },
    isReportPaid() {
      return this.status == "paid";
    }
  }
};
</script>

<style></style>
