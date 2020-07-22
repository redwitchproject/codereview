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

    <div class="mt-4 bg-white shadow px-4 py-5 rounded-lg sm:p-6">
      <div class="md:grid md:grid-cols-3 md:gap-6">
        <div class="md:col-span-3">
          <div class="flex flex-row-reverse">
            <div class="col-span-right text-right ml-auto">
              <button
                v-if="reportIsSubmitted || reportIsApproved || reportIsPaid"
                @click="emailModalIsOpen = true"
                type="button"
                class="ml-2 inline-flex items-center px-2.5 py-1.5 border border-transparent text-xs leading-4 font-medium rounded text-teal-700 bg-teal-100 hover:bg-teal-50 focus:outline-none focus:border-teal-300 focus:shadow-outline-teal active:bg-teal-200 transition ease-in-out duration-150"
              >
                Email report
              </button>
            </div>
            <h3 class="text-lg font-medium leading-6 text-gray-900">History</h3>
          </div>

          <div class="relative m-8 mt-3">
            <div
              class="border-r-2 border-gray-500 absolute h-full top-3"
              style="left: 3px"
            ></div>
            <ul class="list-none m-0 p-0">
              <li class="mb-2">
                <div class="flex items-center mb-1">
                  <div class="bg-gray-500 rounded-full h-2 w-2"></div>
                  <div class="flex-1 ml-4 font-medium text-sm text-gray-700">
                    {{ created }}
                  </div>
                </div>
                <div class="ml-12 text-sm text-gray-700">
                  Expense report created by {{ requestUserName }}
                </div>
              </li>
              <li v-for="event in items" :key="event.id" class="mb-2">
                <div
                  v-if="event.displayTimeline"
                  class="flex items-center mb-1"
                >
                  <div class="bg-gray-500 rounded-full h-2 w-2"></div>
                  <div class="flex-1 ml-4 font-medium text-sm text-gray-700">
                    {{ event.created }}
                  </div>
                </div>
                <div class="ml-12 text-sm text-gray-700">
                  {{ returnHistoryText(event) }}
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>

    <div
      v-if="emailModalIsOpen"
      class="fixed bottom-0 inset-x-0 px-4 pb-4 sm:inset-0 sm:flex sm:items-center sm:justify-center"
    >
      <transition
        enter-active-class="ease-out duration-300"
        enter-class="opacity-0"
        enter-to-class="opacity-100"
        leave-active-class="ease-in duration-200"
        leave-class="opacity-100"
        leave-to-class="opacity-0"
      >
        <div class="fixed inset-0 transition-opacity">
          <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
        </div>
      </transition>
      <transition
        enter-active-class="ease-out duration-300"
        enter-class="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
        enter-to-class="opacity-100 translate-y-0 sm:scale-100"
        leave-active-class="ease-in duration-200"
        leave-class="pacity-100 translate-y-0 sm:scale-100"
        leave-to-class="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
      >
        <div
          class="bg-white rounded-lg px-4 pt-5 pb-4 overflow-hidden shadow-xl transform transition-all sm:w-2/5 sm:p-6"
          role="dialog"
          aria-modal="true"
          aria-labelledby="modal-headline"
        >
          <div class="sm:flex sm:items-start">
            <div class="text-center mt-0 sm:ml-4 sm:text-left">
              <h3
                class="text-md leading-6 font-medium text-gray-900"
                id="modal-headline"
              >
                Complete this form to send the expense report details and
                receipts via email.
              </h3>
              <form @submit.prevent="sendEmail" class="mt-5 sm:mt-3">
                <div class="flex items-center">
                  <label
                    for="toEmail"
                    class="text-gray-700 w-24 sm:w-48 text-sm font-medium"
                    >To Email Address*</label
                  >
                  <input
                    type="email"
                    required
                    v-model="toEmail"
                    class="form-input mt-1 block w-full mx-1 text-sm text-gray-700"
                    id="toEmail"
                    placeholder="Email"
                  />
                </div>
                <div class="flex items-center mt-4">
                  <label
                    for="fromEmail"
                    class="text-gray-700 w-24 sm:w-36 text-sm font-medium"
                    >From Email Address</label
                  >
                  <!-- <input type="email" disabled class="form-input mt-1 block w-full mx-1 text-sm text-gray-700" id="fromEmail" value="no-reply@app.com"> -->
                  <p class="sm:mx-1 text-sm text-gray-500 w-full sm:w-auto">
                    no-reply@app.com
                  </p>
                </div>
                <div class="flex items-center mt-4">
                  <label
                    for="subject"
                    class="text-gray-700 w-24 sm:w-36 text-sm font-medium"
                    >Subject Line</label
                  >
                  <!-- <input type="text" disabled class="form-input mt-1 block w-full mx-1 text-sm text-gray-700" id="subject" :value="returnSubjectLine()"> -->
                  <p class="sm:mx-1 text-sm text-gray-500 w-full sm:w-auto">
                    Expense Report details for {{ user.name }} from
                    {{ user.company }}
                  </p>
                </div>
                <div class="flex items-center mt-4">
                  <label
                    for="emailText"
                    class="text-gray-700 w-24 sm:w-48 text-sm font-medium"
                    >Email Text*</label
                  >
                  <textarea
                    required
                    v-model="emailMessage"
                    id="emailText"
                    cols="30"
                    rows="2"
                    placeholder="Enter text to include in email"
                    class="form-input mt-1 block w-full mx-1 text-sm text-gray-700"
                  ></textarea>
                </div>
                <div class="flex items-center mt-3">
                  <div class="sm:w-36"></div>
                  <input
                    v-model="sendCopyToSender"
                    type="checkbox"
                    class="form-checkbox text-teal-500 w-3 h-3"
                  />
                  <label for="emailCopy" class="text-gray-700 text-sm ml-2"
                    >Would you like a copy of the email?</label
                  >
                </div>

                <div class="mt-5 sm:mt-4 sm:flex sm:flex-row-reverse">
                  <span
                    class="flex w-full rounded-md shadow-sm sm:ml-3 sm:w-auto"
                  >
                    <button
                      type="submit"
                      class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-teal-500 text-base leading-6 font-medium text-white shadow-sm hover:bg-teal-400 focus:outline-none focus:border-teal-600 focus:shadow-outline-teal transition ease-in-out duration-150 sm:text-sm sm:leading-5"
                    >
                      Submit form
                    </button>
                  </span>
                  <span
                    class="mt-3 flex w-full rounded-md shadow-sm sm:mt-0 sm:w-auto"
                  >
                    <button
                      @click="emailModalIsOpen = false"
                      type="button"
                      class="inline-flex justify-center w-full rounded-md border border-gray-300 px-4 py-2 bg-white text-base leading-6 font-medium text-gray-700 shadow-sm hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue transition ease-in-out duration-150 sm:text-sm sm:leading-5"
                    >
                      Cancel
                    </button>
                  </span>
                </div>
              </form>
            </div>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: "History",
  data: () => {
    return {
      emailModalIsOpen: false,
      expenseReportSlug: "",
      showAlert: false,

      toEmail: "",
      emailMessage: "",
      sendCopyToSender: false
    };
  },
  props: [
    "items",
    "created",
    "requestUserName",
    "user",
    "reportTotalAmount",
    "reportTotalAmountCurrency",
    "loggedInUser",
    "userIsRequester",
    "userIsStaff",
    "reportIsSubmitted",
    "reportIsDraft",
    "reportIsWithdrawn",
    "reportIsRejected",
    "reportIsApproved",
    "reportIsPaid"
  ],
  methods: {
    getCookie: function(name) {
      var match = document.cookie.match(new RegExp(name + "=([^;]+)"));
      if (match) return match[1];
      return;
    },
    sendEmail() {
      this.$http
        .post(
          `/api/report/email/${this.expenseReportSlug}/`,
          {
            to_email: this.toEmail,
            message: this.emailMessage,
            send_copy_to_sender: this.sendCopyToSender
          },
          { headers: { "X-CSRFToken": this.getCookie("csrftoken") } }
        )
        .then(function() {
          this.emailModalIsOpen = false;
          this.alertText = "Expense report sent!";
          this.showAlert = true;
          setTimeout(() => (this.showAlert = false), 5000);
          // this.getData();
        })
        .catch(err => {
          console.log(err);
          this.errors = err.body;
          setTimeout(() => (this.errors = []), 5000);
        });
    },
    returnHistoryText(event) {
      if (event.action === "submitted" || event.action === "withdrawn") {
        if (event.requester_note) {
          return `Expense report ${event.action} by ${event.user} with note '${event.requester_note}'`;
        } else {
          return `Expense report ${event.action} by ${event.user}`;
        }
      } else if (event.action === "rejected") {
        if (this.userIsStaff) {
          return `Expense report ${event.action} by ${event.user} with note '${event.comment}'`;
        } else if (this.userIsRequester) {
          return `Expense report ${event.action} with note '${event.comment}'`;
        }
      } else if (event.action === "paid") {
        return `Company paid ${this.addZeroes(this.reportTotalAmount)} ${
          this.reportTotalAmountCurrency
        }`;
      } else if (event.action === "approved") {
        if (this.userIsStaff) {
          if (event.approval_note) {
            return `Expense report ${event.action} by ${event.user} with note '${event.approval_note}'`;
          } else {
            return `Expense report ${event.action} by ${event.user}`;
          }
        } else if (this.userIsRequester) {
          return `Expense report ${event.action}`;
        }
      } else if (event.action === "sent_report") {
        if (event.user == this.$props.loggedInUser) {
          return `${event.user} sent expense report to ${event.comment}`;
        }
      } else if (event.action === "created") {
        return `Expense report ${event.action} by ${event.user}`;
      }
    },
    addZeroes(val) {
      var num = Number(val);
      if (
        String(num).split(".").length < 2 ||
        String(num).split(".")[1].length <= 2
      ) {
        num = num.toFixed(2);
      }
      return num;
    }
  },
  mounted: function() {
    this.expenseReportSlug = window.expenseReportSlug;
  }
};
</script>

<style></style>
