<template>
  <div>
    <div class="bg-white shadow px-4 py-5 rounded-lg sm:p-6">
      <div class="md:grid md:grid-cols-3 md:gap-6">
        <div class="md:col-span-1">
          <h3 class="text-lg font-medium leading-6 text-gray-900">
            Expense Report
          </h3>
        </div>
      </div>
      <div class="md:grid md:grid-cols-3 md:gap-6">
        <div class="md:col-span-1">
          <div class="flex mt-4">
            <p class="text-sm leading-5 text-gray-700 w-32 font-semibold">
              Requester name
            </p>
            <p class="text-sm leading-5 text-gray-700">{{ user.name }}</p>
          </div>
          <div v-if="convertedAmount" class="flex mt-1">
            <p class="text-sm leading-5 text-gray-700 w-32 font-semibold">
              Total amount
            </p>
            <p class="text-sm leading-5 text-teal-600 font-medium">
              {{ addZeroes(convertedAmount) }} {{ convertedCurrency }}
            </p>
          </div>
          <div v-else class="flex mt-1">
            <p class="text-sm leading-5 text-gray-700 w-32 font-semibold">
              Total amount
            </p>
            <p class="text-sm leading-5 text-teal-600 font-medium">
              {{ addZeroes(reportTotalAmount) }}
              {{ reportTotalAmountCurrency }}
            </p>
          </div>
          <div class="flex mt-1">
            <p class="text-sm leading-5 text-gray-700 w-32 font-semibold">
              Status
            </p>
            <span
              class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full"
              :class="statusDisplayStyle"
            >
              {{ statusDisplay }}
            </span>
          </div>
        </div>
        <div v-if="userIsRequester" class="col-span-1"></div>
        <div v-else class="col-span-1">
          <div v-if="user.display_bill_to" class="flex mt-4">
            <p class="text-sm leading-5 text-gray-700 w-32 font-semibold">
              {{ user.bill_to_label }}
            </p>
            <p class="text-sm leading-5 text-gray-700">{{ user.bill_to }}</p>
          </div>
          <div v-if="user.display_bill_to2" class="flex mt-1">
            <p class="text-sm leading-5 text-gray-700 w-32 font-semibold">
              {{ user.bill_to2_label }}
            </p>
            <p class="text-sm leading-5 text-gray-700">
              {{ user.bill_to2 }}
            </p>
          </div>
        </div>
        <div class="col-span-1 ml-auto flex flex-col">
          <div v-if="userIsRequester" class="flex">
            <span
              v-if="reportIsSubmitted"
              class="sm:ml-auto mt-3 sm:mt-0 inline-flex rounded-md shadow-sm"
            >
              <button
                @click="withdraw"
                type="button"
                class="inline-flex items-center px-4 py-2 border border-transparent text-sm leading-5 font-medium rounded-md text-white bg-red-600 hover:bg-red-500 focus:outline-none focus:border-red-700 focus:shadow-outline-red active:bg-red-700 transition ease-in-out duration-150"
              >
                Withdraw
              </button>
            </span>
            <span
              v-tooltip="
                !user.has_reimbursement_method_set
                  ? {
                      content:
                        'Please update your profile information with your reimbursement and notification preferences'
                    }
                  : ''
              "
              v-if="reportIsDraft || reportIsWithdrawn || reportIsRejected"
              class="sm:ml-auto mt-3 sm:mt-0 inline-flex rounded-md shadow-sm"
            >
              <button
                :disabled="!user.has_reimbursement_method_set"
                @click="submitClick"
                type="button"
                :class="
                  !user.has_reimbursement_method_set
                    ? 'opacity-50 cursor-default'
                    : 'hover:bg-teal-400'
                "
                class="inline-flex items-center px-4 py-2 border border-transparent text-sm leading-5 font-medium rounded-md text-white bg-teal-500 focus:outline-none focus:border-teal-600 focus:shadow-outline-teal active:bg-teal-700 transition ease-in-out duration-150"
              >
                Submit
              </button>
            </span>
            <button
              v-if="reportIsDraft || reportIsWithdrawn"
              @click="deleteModalIsOpen = true"
              type="button"
              class="ml-2 mt-3 sm:mt-0 inline-flex items-center px-4 py-2 border border-transparent text-sm leading-5 font-medium rounded-md text-red-700 bg-red-100 hover:bg-red-50 focus:outline-none focus:border-red-300 focus:shadow-outline-red active:bg-red-200 transition ease-in-out duration-150"
            >
              Delete report
            </button>
          </div>
          <div v-if="reportIsSubmitted && userIsStaff" class="flex">
            <span
              class="sm:ml-auto mt-3 sm:mt-0 inline-flex rounded-md shadow-sm"
            >
              <button
                @click="approveModalIsOpen = true"
                type="button"
                class="inline-flex items-center px-4 py-2 border border-transparent text-sm leading-5 font-medium rounded-md text-white bg-teal-500 hover:bg-teal-400 focus:outline-none focus:border-teal-600 focus:shadow-outline-teal active:bg-teal-700 transition ease-in-out duration-150"
              >
                Approve
              </button>
            </span>
            <span class="ml-2 mt-3 sm:mt-0 inline-flex rounded-md shadow-sm">
              <button
                @click="rejectModalIsOpen = true"
                type="button"
                class="inline-flex items-center px-4 py-2 border border-transparent text-sm leading-5 font-medium rounded-md text-white bg-red-600 hover:bg-red-500 focus:outline-none focus:border-red-700 focus:shadow-outline-red active:bg-red-800 transition ease-in-out duration-150"
              >
                Reject
              </button>
            </span>
          </div>
          <div
            v-if="!userIsRequester && !isReportInRequestUserCurrency"
            class="flex items-center sm:ml-auto mt-5 sm:mt-3"
          >
            <div class="flex items-center">
              <span
                role="checkbox"
                id="convert"
                :aria-checked="convert"
                tabindex="0"
                @click="convertCurrency"
                :class="convert ? 'bg-teal-500' : 'bg-gray-200'"
                class="relative inline-block flex-shrink-0 h-6 w-11 border-2 border-transparent rounded-full cursor-pointer transition-colors ease-in-out duration-200 focus:outline-none focus:shadow-outline"
              >
                <span
                  aria-hidden="true"
                  :class="convert ? 'translate-x-5' : 'translate-x-0'"
                  class="inline-block h-5 w-5 rounded-full bg-white shadow transform transition ease-in-out duration-200"
                ></span>
              </span>
            </div>
            <div class="ml-1">
              <label
                for="convert"
                class="text-sm font-medium leading-5 text-gray-700"
                >Convert to {{ requestUserCurrency }}</label
              >
            </div>
          </div>
        </div>
        <!-- <p
          v-for="error in errors"
          :key="error"
          class="px-2 py-1.5 bg-red-100 text-xs text-red-600 border border-red-600 rounded font-semibold"
        >
          {{ error }}
        </p> -->
      </div>
    </div>

    <div
      v-if="submitModalIsOpen"
      class="z-40 fixed bottom-0 inset-x-0 px-4 pb-4 sm:inset-0 sm:flex sm:items-center sm:justify-center"
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
          class="bg-white rounded-lg px-4 pt-5 pb-4 overflow-hidden shadow-xl transform transition-all sm:max-w-xl sm:w-full sm:p-6"
          role="dialog"
          aria-modal="true"
          aria-labelledby="modal-headline"
        >
          <div class="sm:flex sm:items-start">
            <div class="mt-3 text-center sm:mt-0 sm:mx-4 sm:text-left">
              <p class="text-sm leading-5 text-gray-500">
                You have the option to leave a comment or explanation regarding
                your expense report. This will be sent to the approver and be
                attached to your expense report.
              </p>
              <textarea
                v-model="submitNote"
                class="form-textarea mt-3 text-xs w-full"
                name=""
                id=""
                cols="67"
                rows="5"
                placeholder="Notes or comments left here are visible to the company from which you are requesting reimbursement and cannot be deleted"
              ></textarea>
            </div>
          </div>
          <div class="mt-5 sm:mt-4 mx-4 sm:flex sm:flex-row-reverse">
            <span class="flex w-full rounded-md shadow-sm sm:ml-3 sm:w-auto">
              <button
                @click="submit"
                type="button"
                class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-teal-500 text-base leading-6 font-medium text-white shadow-sm hover:bg-teal-400 focus:outline-none focus:border-teal-600 focus:shadow-outline-teal transition ease-in-out duration-150 sm:text-sm sm:leading-5"
              >
                Submit for approval
              </button>
            </span>
            <span
              class="mt-3 flex w-full rounded-md shadow-sm sm:mt-0 sm:w-auto"
            >
              <button
                @click="submitModalIsOpen = false"
                type="button"
                class="inline-flex justify-center w-full rounded-md border border-gray-300 px-4 py-2 bg-white text-base leading-6 font-medium text-gray-700 shadow-sm hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue transition ease-in-out duration-150 sm:text-sm sm:leading-5"
              >
                Cancel
              </button>
            </span>
          </div>
        </div>
      </transition>
    </div>

    <div
      v-if="alcoholModalIsOpen"
      class="z-40 fixed bottom-0 inset-x-0 px-4 pb-4 sm:inset-0 sm:flex sm:items-center sm:justify-center"
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
          class="relative bg-white rounded-lg px-4 pt-5 pb-4 overflow-hidden shadow-xl transform transition-all sm:max-w-lg sm:w-full sm:p-6"
          role="dialog"
          aria-modal="true"
          aria-labelledby="modal-headline"
        >
          <div class="hidden sm:block absolute top-0 right-0 pt-4 pr-4">
            <button
              @click="alcoholModalIsOpen = false"
              type="button"
              class="text-gray-400 hover:text-gray-500 focus:outline-none focus:text-gray-500 transition ease-in-out duration-150"
              aria-label="Close"
            >
              <svg
                class="h-6 w-6"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M6 18L18 6M6 6l12 12"
                />
              </svg>
            </button>
          </div>
          <div class="sm:flex sm:items-start">
            <div class="mt-3 text-center sm:mt-0 sm:ml-3 sm:text-left">
              <h3
                class="text-lg leading-6 font-medium text-gray-900"
                id="modal-headline"
              >
                Alcohol Expense Confirmation
              </h3>
              <div class="mt-2">
                <p class="text-sm leading-5 text-gray-500">
                  Per company policy, alcohol charges are not reimbursed. Is
                  your expense report clear of this type of expense and ready to
                  submit?
                </p>
              </div>
            </div>
          </div>
          <div class="mt-5 sm:mt-4 sm:flex sm:flex-row-reverse">
            <span class="flex w-full rounded-md shadow-sm sm:ml-3 sm:w-auto">
              <button
                @click="confirmedNoAlcohol"
                type="button"
                class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-teal-500 text-base leading-6 font-medium text-white shadow-sm hover:bg-teal-400 focus:outline-none focus:border-teal-600 focus:shadow-outline-teal transition ease-in-out duration-150 sm:text-sm sm:leading-5"
              >
                Yes
              </button>
            </span>
            <span
              class="mt-3 flex w-full rounded-md shadow-sm sm:mt-0 sm:w-auto"
            >
              <button
                @click="alcoholModalIsOpen = false"
                type="button"
                class="inline-flex justify-center w-full rounded-md border border-gray-300 px-4 py-2 bg-white text-base leading-6 font-medium text-gray-700 shadow-sm hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue transition ease-in-out duration-150 sm:text-sm sm:leading-5"
              >
                No
              </button>
            </span>
          </div>
        </div>
      </transition>
    </div>

    <div
      v-if="deleteModalIsOpen"
      class="z-40 fixed bottom-0 inset-x-0 px-4 pb-4 sm:inset-0 sm:flex sm:items-center sm:justify-center"
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
          class="relative bg-white rounded-lg px-4 pt-5 pb-4 overflow-hidden shadow-xl transform transition-all sm:max-w-lg sm:w-full sm:p-6"
          role="dialog"
          aria-modal="true"
          aria-labelledby="modal-headline"
        >
          <div class="hidden sm:block absolute top-0 right-0 pt-4 pr-4">
            <button
              @click="deleteModalIsOpen = false"
              type="button"
              class="text-gray-400 hover:text-gray-500 focus:outline-none focus:text-gray-500 transition ease-in-out duration-150"
              aria-label="Close"
            >
              <svg
                class="h-6 w-6"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M6 18L18 6M6 6l12 12"
                />
              </svg>
            </button>
          </div>
          <div class="sm:flex sm:items-start">
            <div class="mt-3 text-center sm:mt-0 sm:ml-3 sm:text-left">
              <h3
                class="text-lg leading-6 font-medium text-gray-900"
                id="modal-headline"
              >
                Delete Expense Report
              </h3>
              <div class="mt-2">
                <p class="text-sm leading-5 text-gray-500">
                  This will delete all of the contents of this expense report.
                  This action cannot be reversed. Are you sure?
                </p>
              </div>
            </div>
          </div>
          <div class="mt-5 sm:mt-4 sm:flex sm:flex-row-reverse">
            <span class="flex w-full rounded-md shadow-sm sm:ml-3 sm:w-auto">
              <button
                @click="deleteReport"
                type="button"
                class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-red-600 text-base leading-6 font-medium text-white shadow-sm hover:bg-red-500 focus:outline-none focus:border-red-700 focus:shadow-outline-red transition ease-in-out duration-150 sm:text-sm sm:leading-5"
              >
                Yes
              </button>
            </span>
            <span
              class="mt-3 flex w-full rounded-md shadow-sm sm:mt-0 sm:w-auto"
            >
              <button
                @click="deleteModalIsOpen = false"
                type="button"
                class="inline-flex justify-center w-full rounded-md border border-gray-300 px-4 py-2 bg-white text-base leading-6 font-medium text-gray-700 shadow-sm hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue transition ease-in-out duration-150 sm:text-sm sm:leading-5"
              >
                Cancel
              </button>
            </span>
          </div>
        </div>
      </transition>
    </div>

    <div
      v-if="rejectModalIsOpen"
      class="z-40 fixed bottom-0 inset-x-0 px-4 pb-4 sm:inset-0 sm:flex sm:items-center sm:justify-center"
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
          class="bg-white rounded-lg px-4 pt-5 pb-4 overflow-hidden shadow-xl transform transition-all sm:max-w-xl sm:w-full sm:p-6"
          role="dialog"
          aria-modal="true"
          aria-labelledby="modal-headline"
        >
          <div class="">
            <div class="mt-3 text-center sm:mt-0 sm:mx-4 sm:text-left">
              <p class="text-sm leading-5 text-gray-500">
                Are you sure you want to reject this expense report for
                {{ addZeroes(reportTotalAmount) }}
                {{ reportTotalAmountCurrency }}?
              </p>
              <textarea
                v-model="rejectNote"
                class="form-textarea mt-3 text-xs w-full"
                name=""
                id=""
                cols="67"
                rows="5"
                placeholder="Provide a reason for the rejection. This will be included in the notification to the requester."
              ></textarea>
              <p
                v-if="rejectionError"
                class="text-red-600 text-xs font-semibold"
              >
                This field may not be blank.
              </p>
            </div>
          </div>
          <div class="mt-5 sm:mt-4 mx-4 sm:flex sm:flex-row-reverse">
            <span class="flex w-full rounded-md shadow-sm sm:ml-3 sm:w-auto">
              <button
                @click="reject"
                type="button"
                class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-red-500 text-base leading-6 font-medium text-white shadow-sm hover:bg-red-400 focus:outline-none focus:border-red-600 focus:shadow-outline-red transition ease-in-out duration-150 sm:text-sm sm:leading-5"
              >
                Reject
              </button>
            </span>
            <span
              class="mt-3 flex w-full rounded-md shadow-sm sm:mt-0 sm:w-auto"
            >
              <button
                @click="rejectModalIsOpen = false"
                type="button"
                class="inline-flex justify-center w-full rounded-md border border-gray-300 px-4 py-2 bg-white text-base leading-6 font-medium text-gray-700 shadow-sm hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue transition ease-in-out duration-150 sm:text-sm sm:leading-5"
              >
                Cancel
              </button>
            </span>
          </div>
        </div>
      </transition>
    </div>

    <div
      v-if="approveModalIsOpen"
      class="z-40 fixed bottom-0 inset-x-0 px-4 pb-4 sm:inset-0 sm:flex sm:items-center sm:justify-center"
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
          class="bg-white rounded-lg px-4 pt-5 pb-4 overflow-hidden shadow-xl transform transition-all sm:max-w-xl sm:w-full sm:p-6"
          role="dialog"
          aria-modal="true"
          aria-labelledby="modal-headline"
        >
          <div class="sm:flex sm:items-start">
            <div class="mt-3 text-center sm:mt-0 sm:mx-4 sm:text-left">
              <p class="text-sm leading-5 text-gray-500 max-w-full">
                Are you sure you want to approve this expense report for
                {{ addZeroes(reportTotalAmount) }}
                {{ reportTotalAmountCurrency }}? Once approved the reimbursement
                will be processed within 2 business days.
              </p>
              <textarea
                v-model="approveNote"
                class="form-textarea mt-3 text-xs w-full"
                name=""
                id=""
                cols="67"
                rows="5"
                placeholder="Comments are for internal use only. They will not be displayed to the requester."
              ></textarea>
            </div>
          </div>
          <div class="mt-5 sm:mt-4 mx-4 sm:flex sm:flex-row-reverse">
            <span class="flex w-full rounded-md shadow-sm sm:ml-3 sm:w-auto">
              <button
                @click="approve"
                type="button"
                class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-teal-500 text-base leading-6 font-medium text-white shadow-sm hover:bg-teal-400 focus:outline-none focus:border-teal-600 focus:shadow-outline-teal transition ease-in-out duration-150 sm:text-sm sm:leading-5"
              >
                Approve
              </button>
            </span>
            <span
              class="mt-3 flex w-full rounded-md shadow-sm sm:mt-0 sm:w-auto"
            >
              <button
                @click="approveModalIsOpen = false"
                type="button"
                class="inline-flex justify-center w-full rounded-md border border-gray-300 px-4 py-2 bg-white text-base leading-6 font-medium text-gray-700 shadow-sm hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue transition ease-in-out duration-150 sm:text-sm sm:leading-5"
              >
                Cancel
              </button>
            </span>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: "GeneralInfo",
  data: function() {
    return {
      submitModalIsOpen: false,
      alcoholModalIsOpen: false,
      deleteModalIsOpen: false,
      rejectModalIsOpen: false,
      approveModalIsOpen: false,
      submitNote: "",
      rejectNote: "",
      approveNote: "",
      convert: false
    };
  },
  props: [
    "user",
    "reportTotalAmount",
    "reportTotalAmountCurrency",
    "status",
    "statusDisplay",
    "requestUserCurrency",
    "expenses",
    "convertedAmount",
    "convertedCurrency",
    "convertedExpenses",
    "isReportInRequestUserCurrency",
    "userIsRequester",
    "userIsStaff",
    "reportIsSubmitted",
    "reportIsDraft",
    "reportIsWithdrawn",
    "reportIsRejected",
    "reportIsApproved",
    "reportIsPaid",
    "rejectionError"
  ],
  methods: {
    addZeroes(val) {
      var num = Number(val);
      if (
        String(num).split(".").length < 2 ||
        String(num).split(".")[1].length <= 2
      ) {
        num = num.toFixed(2);
      }
      return num;
    },
    submit() {
      this.$emit("submitReport", this.submitNote);
      this.submitModalIsOpen = false;
      this.submitNote = "";
    },
    withdraw() {
      this.$emit("withdrawReport");
    },
    deleteReport() {
      this.$emit("deleteReport");
    },
    reject() {
      this.$emit("rejectReport", this.rejectNote);
      this.rejectModalIsOpen = false;
      this.rejectNote = "";
    },
    approve() {
      this.$emit("approveReport", this.approveNote);
      this.approveModalIsOpen = false;
      this.approveNote = "";
    },
    convertCurrency() {
      this.convert = !this.convert;
      this.$emit("converyCurrency", this.convert);
    },
    submitClick() {
      if (this.containsMealExpense()) {
        this.alcoholModalIsOpen = true;
      } else {
        this.submitModalIsOpen = true;
      }
    },
    containsMealExpense() {
      return this.expenses.some(expense => expense["expense_type"] === "meals");
    },
    confirmedNoAlcohol() {
      this.alcoholModalIsOpen = false;
      this.submitModalIsOpen = true;
    },
    checkRejectionModal() {
      if (this.rejectionError) {
        this.rejectModalIsOpen = true;
      }
    }
  },
  mounted: function() {},
  computed: {
    statusDisplayStyle() {
      switch (this.$props.status) {
        case "approved":
          return "bg-blue-100 text-blue-800";
        case "rejected":
          return "bg-red-100 text-red-800";
        case "submitted":
          return "bg-orange-100 text-orange-800";
        case "paid":
          return "bg-green-100 text-green-800";
        case "draft":
          return "bg-gray-100 text-gray-800";
        case "withdrawn":
          return "bg-gray-100 text-gray-800";
        default:
          return "";
      }
    }
  },
  watch: {
    rejectionError: function() {
      this.rejectModalIsOpen = true;
    }
  }
};
</script>

<style></style>
