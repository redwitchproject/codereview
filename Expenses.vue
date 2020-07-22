<template>
  <div>
    <div class="mt-4 bg-white shadow px-4 py-5 rounded-lg sm:p-6">
      <div class="md:grid md:grid-cols-3 md:gap-6 flex">
        <div class="md:col-span-1 flex items-center">
          <h3 class="text-lg font-medium leading-6 text-gray-900">Expenses</h3>
        </div>
        <div class="sm:col-span-1"></div>
        <div v-if="userIsRequester" class="sm:col-span-1 flex ml-auto">
          <span
            v-if="reportIsDraft || reportIsWithdrawn || reportIsRejected"
            class="inline-flex ml-auto rounded-md shadow-sm"
          >
            <a
              href="/expenses/~new"
              type="button"
              class="inline-flex items-center px-2.5 py-1.5 border border-transparent text-xs leading-4 font-medium rounded text-white bg-teal-500 hover:bg-teal-400 focus:outline-none focus:border-teal-600 focus:shadow-outline-teal active:bg-teal-600 transition ease-in-out duration-150"
            >
              Add new expense
            </a>
          </span>
        </div>
      </div>

      <div class="flex flex-col mt-4">
        <div
          class="-my-2 py-2 overflow-x-auto sm:-mx-6 sm:px-6 lg:-mx-8 lg:px-8 "
        >
          <div
            class="align-middle inline-block min-w-full shadow overflow-scroll sm:rounded-lg border-b border-gray-200 max-w-full"
          >
            <table class="min-w-full">
              <thead>
                <tr>
                  <th
                    @click="sort('expense_date')"
                    class="min-h-full min-w-full px-6 py-3 border-b border-gray-200 bg-gray-50 text-left text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider"
                  >
                    <div class="flex items-center cursor-pointer">
                      <span>Date</span>
                      <svg
                        class="ml-1 text-gray-300  h-3 w-3"
                        fill="currentColor"
                        xmlns="http://www.w3.org/2000/svg"
                        xmlns:xlink="http://www.w3.org/1999/xlink"
                        viewBox="0 0 100 100"
                      >
                        <path
                          d="M49.924,44.84c-8.066,0-16.133,0.016-24.199-0.017c-1.109-0.005-2.24-0.152-3.322-0.399  c-2.906-0.664-4.182-3.037-3.03-5.801c0.525-1.258,1.273-2.489,2.166-3.519c7.25-8.37,14.543-16.702,21.866-25.008  c0.908-1.03,1.992-1.948,3.125-2.729c2.267-1.566,4.693-1.626,6.913,0.017c1.528,1.131,2.922,2.484,4.202,3.894  c3.311,3.646,6.537,7.368,9.774,11.081c3.757,4.311,7.509,8.624,11.216,12.979c1.178,1.383,2.217,2.892,2.314,4.822  c0.102,2.034-0.784,3.476-2.746,4.056c-1.217,0.36-2.517,0.59-3.782,0.599C66.255,44.866,58.089,44.84,49.924,44.84z"
                        ></path>
                        <path
                          d="M50.008,55.16c8,0.001,16-0.025,24,0.026c1.364,0.009,2.771,0.198,4.081,0.575  c2.402,0.689,3.556,2.79,2.647,5.116c-0.634,1.624-1.595,3.208-2.733,4.531c-6.903,8.014-13.879,15.965-20.884,23.892  c-1.105,1.251-2.385,2.396-3.745,3.364c-2.179,1.552-4.54,1.544-6.745,0.023c-1.213-0.837-2.376-1.816-3.349-2.918  c-7.24-8.203-14.437-16.444-21.614-24.702c-0.794-0.913-1.496-1.95-2.039-3.032c-1.669-3.327-0.081-6.213,3.623-6.657  c1.142-0.139,2.3-0.207,3.45-0.209C34.47,55.152,42.239,55.16,50.008,55.16z"
                        ></path>
                      </svg>
                    </div>
                  </th>
                  <th
                    @click="sort('expense_type')"
                    class="px-6 py-3 border-b border-gray-200 bg-gray-50 text-left text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider"
                  >
                    <div class="flex items-center cursor-pointer">
                      <span>Type</span>
                      <svg
                        class="ml-1 text-gray-300 h-3 w-3"
                        fill="currentColor"
                        xmlns="http://www.w3.org/2000/svg"
                        xmlns:xlink="http://www.w3.org/1999/xlink"
                        viewBox="0 0 100 100"
                      >
                        <path
                          d="M49.924,44.84c-8.066,0-16.133,0.016-24.199-0.017c-1.109-0.005-2.24-0.152-3.322-0.399  c-2.906-0.664-4.182-3.037-3.03-5.801c0.525-1.258,1.273-2.489,2.166-3.519c7.25-8.37,14.543-16.702,21.866-25.008  c0.908-1.03,1.992-1.948,3.125-2.729c2.267-1.566,4.693-1.626,6.913,0.017c1.528,1.131,2.922,2.484,4.202,3.894  c3.311,3.646,6.537,7.368,9.774,11.081c3.757,4.311,7.509,8.624,11.216,12.979c1.178,1.383,2.217,2.892,2.314,4.822  c0.102,2.034-0.784,3.476-2.746,4.056c-1.217,0.36-2.517,0.59-3.782,0.599C66.255,44.866,58.089,44.84,49.924,44.84z"
                        ></path>
                        <path
                          d="M50.008,55.16c8,0.001,16-0.025,24,0.026c1.364,0.009,2.771,0.198,4.081,0.575  c2.402,0.689,3.556,2.79,2.647,5.116c-0.634,1.624-1.595,3.208-2.733,4.531c-6.903,8.014-13.879,15.965-20.884,23.892  c-1.105,1.251-2.385,2.396-3.745,3.364c-2.179,1.552-4.54,1.544-6.745,0.023c-1.213-0.837-2.376-1.816-3.349-2.918  c-7.24-8.203-14.437-16.444-21.614-24.702c-0.794-0.913-1.496-1.95-2.039-3.032c-1.669-3.327-0.081-6.213,3.623-6.657  c1.142-0.139,2.3-0.207,3.45-0.209C34.47,55.152,42.239,55.16,50.008,55.16z"
                        ></path>
                      </svg>
                    </div>
                  </th>
                  <th
                    @click="sort('vendor')"
                    class="px-6 py-3 border-b border-gray-200 bg-gray-50 text-left text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider"
                  >
                    <div class="flex items-center cursor-pointer">
                      <span>Vendor</span>
                      <svg
                        class="text-gray-300 ml-1 h-3 w-3"
                        fill="currentColor"
                        xmlns="http://www.w3.org/2000/svg"
                        xmlns:xlink="http://www.w3.org/1999/xlink"
                        viewBox="0 0 100 100"
                      >
                        <path
                          d="M49.924,44.84c-8.066,0-16.133,0.016-24.199-0.017c-1.109-0.005-2.24-0.152-3.322-0.399  c-2.906-0.664-4.182-3.037-3.03-5.801c0.525-1.258,1.273-2.489,2.166-3.519c7.25-8.37,14.543-16.702,21.866-25.008  c0.908-1.03,1.992-1.948,3.125-2.729c2.267-1.566,4.693-1.626,6.913,0.017c1.528,1.131,2.922,2.484,4.202,3.894  c3.311,3.646,6.537,7.368,9.774,11.081c3.757,4.311,7.509,8.624,11.216,12.979c1.178,1.383,2.217,2.892,2.314,4.822  c0.102,2.034-0.784,3.476-2.746,4.056c-1.217,0.36-2.517,0.59-3.782,0.599C66.255,44.866,58.089,44.84,49.924,44.84z"
                        ></path>
                        <path
                          d="M50.008,55.16c8,0.001,16-0.025,24,0.026c1.364,0.009,2.771,0.198,4.081,0.575  c2.402,0.689,3.556,2.79,2.647,5.116c-0.634,1.624-1.595,3.208-2.733,4.531c-6.903,8.014-13.879,15.965-20.884,23.892  c-1.105,1.251-2.385,2.396-3.745,3.364c-2.179,1.552-4.54,1.544-6.745,0.023c-1.213-0.837-2.376-1.816-3.349-2.918  c-7.24-8.203-14.437-16.444-21.614-24.702c-0.794-0.913-1.496-1.95-2.039-3.032c-1.669-3.327-0.081-6.213,3.623-6.657  c1.142-0.139,2.3-0.207,3.45-0.209C34.47,55.152,42.239,55.16,50.008,55.16z"
                        ></path>
                      </svg>
                    </div>
                  </th>
                  <th
                    @click="sort('amount')"
                    class="px-6 py-3 border-b border-gray-200 bg-gray-50 text-left text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider"
                  >
                    <div class="flex items-center cursor-pointer">
                      <span>Amount</span>
                      <svg
                        class="text-gray-300 ml-1 h-3 w-3"
                        fill="currentColor"
                        xmlns="http://www.w3.org/2000/svg"
                        xmlns:xlink="http://www.w3.org/1999/xlink"
                        viewBox="0 0 100 100"
                      >
                        <path
                          d="M49.924,44.84c-8.066,0-16.133,0.016-24.199-0.017c-1.109-0.005-2.24-0.152-3.322-0.399  c-2.906-0.664-4.182-3.037-3.03-5.801c0.525-1.258,1.273-2.489,2.166-3.519c7.25-8.37,14.543-16.702,21.866-25.008  c0.908-1.03,1.992-1.948,3.125-2.729c2.267-1.566,4.693-1.626,6.913,0.017c1.528,1.131,2.922,2.484,4.202,3.894  c3.311,3.646,6.537,7.368,9.774,11.081c3.757,4.311,7.509,8.624,11.216,12.979c1.178,1.383,2.217,2.892,2.314,4.822  c0.102,2.034-0.784,3.476-2.746,4.056c-1.217,0.36-2.517,0.59-3.782,0.599C66.255,44.866,58.089,44.84,49.924,44.84z"
                        ></path>
                        <path
                          d="M50.008,55.16c8,0.001,16-0.025,24,0.026c1.364,0.009,2.771,0.198,4.081,0.575  c2.402,0.689,3.556,2.79,2.647,5.116c-0.634,1.624-1.595,3.208-2.733,4.531c-6.903,8.014-13.879,15.965-20.884,23.892  c-1.105,1.251-2.385,2.396-3.745,3.364c-2.179,1.552-4.54,1.544-6.745,0.023c-1.213-0.837-2.376-1.816-3.349-2.918  c-7.24-8.203-14.437-16.444-21.614-24.702c-0.794-0.913-1.496-1.95-2.039-3.032c-1.669-3.327-0.081-6.213,3.623-6.657  c1.142-0.139,2.3-0.207,3.45-0.209C34.47,55.152,42.239,55.16,50.008,55.16z"
                        ></path>
                      </svg>
                    </div>
                  </th>
                  <th
                    @click="sort('currency')"
                    class="px-6 py-3 border-b border-gray-200 bg-gray-50 text-left text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider"
                  >
                    <div class="flex items-center cursor-pointer">
                      <span>Currency</span>
                      <svg
                        class="text-gray-300 ml-1 h-3 w-3"
                        fill="currentColor"
                        xmlns="http://www.w3.org/2000/svg"
                        xmlns:xlink="http://www.w3.org/1999/xlink"
                        viewBox="0 0 100 100"
                      >
                        <path
                          d="M49.924,44.84c-8.066,0-16.133,0.016-24.199-0.017c-1.109-0.005-2.24-0.152-3.322-0.399  c-2.906-0.664-4.182-3.037-3.03-5.801c0.525-1.258,1.273-2.489,2.166-3.519c7.25-8.37,14.543-16.702,21.866-25.008  c0.908-1.03,1.992-1.948,3.125-2.729c2.267-1.566,4.693-1.626,6.913,0.017c1.528,1.131,2.922,2.484,4.202,3.894  c3.311,3.646,6.537,7.368,9.774,11.081c3.757,4.311,7.509,8.624,11.216,12.979c1.178,1.383,2.217,2.892,2.314,4.822  c0.102,2.034-0.784,3.476-2.746,4.056c-1.217,0.36-2.517,0.59-3.782,0.599C66.255,44.866,58.089,44.84,49.924,44.84z"
                        ></path>
                        <path
                          d="M50.008,55.16c8,0.001,16-0.025,24,0.026c1.364,0.009,2.771,0.198,4.081,0.575  c2.402,0.689,3.556,2.79,2.647,5.116c-0.634,1.624-1.595,3.208-2.733,4.531c-6.903,8.014-13.879,15.965-20.884,23.892  c-1.105,1.251-2.385,2.396-3.745,3.364c-2.179,1.552-4.54,1.544-6.745,0.023c-1.213-0.837-2.376-1.816-3.349-2.918  c-7.24-8.203-14.437-16.444-21.614-24.702c-0.794-0.913-1.496-1.95-2.039-3.032c-1.669-3.327-0.081-6.213,3.623-6.657  c1.142-0.139,2.3-0.207,3.45-0.209C34.47,55.152,42.239,55.16,50.008,55.16z"
                        ></path>
                      </svg>
                    </div>
                  </th>
                  <th
                    class="px-6 py-3 border-b border-gray-200 bg-gray-50 text-left text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider"
                  >
                    Description
                  </th>
                  <th
                    class="px-6 py-3 border-b border-gray-200 bg-gray-50 text-left text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider"
                  >
                    Receipt
                  </th>
                  <th
                    v-if="
                      userIsRequester && expenses[0] && expenses[0].is_editable
                    "
                    class="px-6 py-3 border-b border-gray-200 bg-gray-50 text-left text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider"
                  >
                    Actions
                  </th>
                  <th
                    v-if="userIsStaff"
                    class="px-6 py-3 border-b border-gray-200 bg-gray-50 text-left text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider"
                  >
                    More info
                  </th>
                </tr>
              </thead>
              <tbody class="bg-white">
                <tr
                  v-for="expense in sortedExpenses"
                  :key="expense.transaction_id"
                >
                  <td
                    class="px-6 py-4 whitespace-no-wrap border-b border-gray-200 text-sm leading-5 font-medium text-gray-900"
                  >
                    <a
                      :href="'/expenses/' + expense.transaction_id"
                      class="text-blue-500 hover:underline hover:text-blue-600"
                      >{{ expense.expense_date }}</a
                    >
                  </td>
                  <td
                    class="px-6 py-4 whitespace-no-wrap border-b border-gray-200 text-sm leading-5 text-gray-500"
                  >
                    {{ expense.expense_type_display }}
                  </td>
                  <td
                    class="px-6 py-4 whitespace-no-wrap border-b border-gray-200 text-sm leading-5 text-gray-500"
                  >
                    <span v-if="expense.vendor">{{
                      capitalizeFirstLetter(expense.vendor)
                    }}</span>
                  </td>
                  <td
                    class="px-6 py-4 whitespace-no-wrap border-b border-gray-200 text-sm leading-5 text-gray-500"
                  >
                    {{ addZeroes(expense.amount) }}
                  </td>
                  <td
                    class="px-6 py-4 whitespace-no-wrap border-b border-gray-200 text-sm leading-5 text-gray-500"
                  >
                    {{ expense.currency }}
                  </td>
                  <td
                    class="px-6 py-4 whitespace-no-wrap border-b border-gray-200 text-sm leading-5 text-gray-500"
                  >
                    <span
                      v-tooltip.bottom="tooltipText('description', expense)"
                      v-if="expense.description"
                      >{{
                        expense.description.length > 24
                          ? expense.description.substring(0, 25) + "..."
                          : expense.description
                      }}</span
                    >
                    <span v-else>-</span>
                  </td>
                  <td
                    class="px-6 py-4 whitespace-no-wrap border-b border-gray-200 text-sm leading-5 text-gray-500"
                  >
                    <span
                      v-if="expense.receipt && expense.is_receipt_image"
                      class="inline-flex rounded-md shadow-sm"
                    >
                      <button
                        @click="showImage(expense.receipt)"
                        type="button"
                        class="inline-flex items-center px-2.5 py-1.5 border border-gray-300 text-xs leading-4 font-medium rounded text-gray-700 bg-white hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue active:text-gray-800 active:bg-gray-50 transition ease-in-out duration-150"
                      >
                        View
                      </button>
                    </span>
                    <span
                      v-else-if="expense.receipt && !expense.is_receipt_image"
                      class="inline-flex rounded-md shadow-sm"
                    >
                      <a
                        :href="expense.receipt"
                        target="_blank"
                        type="button"
                        class="inline-flex items-center px-2.5 py-1.5 border border-gray-300 text-xs leading-4 font-medium rounded text-gray-700 bg-white hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue active:text-gray-800 active:bg-gray-50 transition ease-in-out duration-150"
                      >
                        View
                      </a>
                    </span>
                    <span
                      v-else
                      class="py-4 whitespace-no-wrap text-sm leading-5 text-gray-500"
                      >No receipt provided</span
                    >
                  </td>
                  <td
                    v-if="userIsRequester && expense.is_editable"
                    class="px-6 py-4 whitespace-no-wrap border-b border-gray-200 text-sm leading-5 text-gray-500"
                  >
                    <div class="flex items-center">
                      <a :href="returnUrl(expense.transaction_id)" class="">
                        <svg
                          class="h-5 w-5 text-gray-700 hover:text-gray-800"
                          fill="currentColor"
                          xmlns="http://www.w3.org/2000/svg"
                          viewBox="0 0 24 24"
                        >
                          <path
                            class="heroicon-ui"
                            d="M6.3 12.3l10-10a1 1 0 0 1 1.4 0l4 4a1 1 0 0 1 0 1.4l-10 10a1 1 0 0 1-.7.3H7a1 1 0 0 1-1-1v-4a1 1 0 0 1 .3-.7zM8 16h2.59l9-9L17 4.41l-9 9V16zm10-2a1 1 0 0 1 2 0v6a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V6c0-1.1.9-2 2-2h6a1 1 0 0 1 0 2H4v14h14v-6z"
                          />
                        </svg>
                      </a>
                      <button
                        @click="deleteExpenseIconClick(expense.transaction_id)"
                        class=""
                      >
                        <svg
                          class="ml-2 h-5 w-5 text-gray-700 hover:text-gray-800"
                          fill="currentColor"
                          xmlns="http://www.w3.org/2000/svg"
                          viewBox="0 0 24 24"
                        >
                          <path
                            class="heroicon-ui"
                            d="M8 6V4c0-1.1.9-2 2-2h4a2 2 0 0 1 2 2v2h5a1 1 0 0 1 0 2h-1v12a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V8H3a1 1 0 1 1 0-2h5zM6 8v12h12V8H6zm8-2V4h-4v2h4zm-4 4a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0v-6a1 1 0 0 1 1-1zm4 0a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0v-6a1 1 0 0 1 1-1z"
                          />
                        </svg>
                      </button>
                    </div>
                  </td>
                  <td
                    v-if="userIsStaff"
                    class="px-6 py-4 whitespace-no-wrap border-b border-gray-200 text-sm leading-5 text-gray-500"
                  >
                    <div class="flex items-center">
                      <svg
                        v-if="
                          expense.expense_type === 'mileage' ||
                            expense.expense_type === 'mileage_kilometers' ||
                            expense.expense_type === 'lodging'
                        "
                        v-tooltip.bottom="tooltipText('info', expense)"
                        class="inline-block h-4 w-4 text-gray-700"
                        fill="currentColor"
                        xmlns="http://www.w3.org/2000/svg"
                        xmlns:xlink="http://www.w3.org/1999/xlink"
                        viewBox="0 0 20 20"
                      >
                        <path
                          d="M10,0.4c-5.303,0-9.601,4.298-9.601,9.6c0,5.303,4.298,9.601,9.601,9.601c5.301,0,9.6-4.298,9.6-9.601 C19.6,4.698,15.301,0.4,10,0.4z M10.896,3.866c0.936,0,1.211,0.543,1.211,1.164c0,0.775-0.62,1.492-1.679,1.492 c-0.886,0-1.308-0.445-1.282-1.182C9.146,4.719,9.665,3.866,10.896,3.866z M8.498,15.75c-0.64,0-1.107-0.389-0.66-2.094l0.733-3.025 c0.127-0.484,0.148-0.678,0-0.678c-0.191,0-1.022,0.334-1.512,0.664L6.74,10.094c1.555-1.299,3.343-2.061,4.108-2.061 c0.64,0,0.746,0.756,0.427,1.92l-0.84,3.18c-0.149,0.562-0.085,0.756,0.064,0.756c0.192,0,0.82-0.232,1.438-0.719l0.362,0.486 C10.786,15.168,9.137,15.75,8.498,15.75z"
                        />
                      </svg>
                      <svg
                        v-if="expense.exceeds_policy_limit"
                        v-tooltip.bottom="tooltipText('warning', expense)"
                        class="inline-block ml-2 h-4 w-4 text-orange-400"
                        fill="currentColor"
                        xmlns="http://www.w3.org/2000/svg"
                        xmlns:xlink="http://www.w3.org/1999/xlink"
                        viewBox="0 0 20 20"
                      >
                        <path
                          d="M19.511,17.98L10.604,1.348C10.48,1.133,10.25,1,10,1C9.749,1,9.519,1.133,9.396,1.348L0.49,17.98 c-0.121,0.211-0.119,0.471,0.005,0.68C0.62,18.871,0.847,19,1.093,19h17.814c0.245,0,0.474-0.129,0.598-0.34 C19.629,18.451,19.631,18.191,19.511,17.98z M11,17H9v-2h2V17z M11,13.5H9V7h2V13.5z"
                        />
                      </svg>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>

    <div
      v-if="deleteExpenseModalIsOpen"
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
          class="relative bg-white rounded-lg px-4 pt-5 pb-4 overflow-hidden shadow-xl transform transition-all sm:max-w-lg sm:w-full sm:p-6"
          role="dialog"
          aria-modal="true"
          aria-labelledby="modal-headline"
        >
          <div class="hidden sm:block absolute top-0 right-0 pt-4 pr-4">
            <button
              @click="deleteExpenseModalIsOpen = false"
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
                Delete Expense
              </h3>
              <div class="mt-2">
                <p class="text-sm leading-5 text-gray-500">
                  This will delete this expense item including the receipt if
                  attached. This action cannot be reversed. Are you sure?
                </p>
              </div>
            </div>
          </div>
          <div class="mt-5 sm:mt-4 sm:flex sm:flex-row-reverse">
            <span class="flex w-full rounded-md shadow-sm sm:ml-3 sm:w-auto">
              <button
                @click="deleteExpenseItem(deleteExpenseId)"
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
                @click="deleteExpenseModalIsOpen = false"
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
import _ from "lodash";
import Viewer from "viewerjs/dist/viewer.esm";
import "viewerjs/dist/viewer.css";

export default {
  name: "Expenses",
  data: function() {
    return {
      currentSort: "expense_date",
      currentSortDir: "asc",
      deleteExpenseId: null,
      deleteExpenseModalIsOpen: false
    };
  },
  props: [
    "expenses",
    "convertedExpenses",
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
    tooltipText: function(type, expense) {
      if (type === "info") {
        if (expense.expense_type === "mileage") {
          return `Miles: ${expense.miles}, Mileage rate: ${expense.miles_rate}`;
        } else if (expense.expense_type === "mileage_kilometers") {
          return `KM: ${expense.miles}, Mileage rate: ${expense.miles_rate}`;
        } else if (expense.expense_type === "lodging") {
          return `Nights: ${expense.nights}`;
        }
      } else if (type === "warning") {
        return "This line item exceeds the company reimbursement policy";
      } else if (type === "description") {
        if (expense.description.length > 24) {
          return `${expense.description}`;
        }
      }
    },
    capitalizeFirstLetter(word) {
      return word.charAt(0).toUpperCase() + word.slice(1).replace(/_/g, " ");
    },
    showImage(img) {
      var image = new Image();
      image.src = img;

      var viewer = new Viewer(image, {
        hidden: function() {
          viewer.destroy();
        },
        navbar: false,
        toolbar: false,
        title: false,
        movable: false
      });

      viewer.show();
    },
    deleteExpenseIconClick(id) {
      this.deleteExpenseModalIsOpen = true;
      this.deleteExpenseId = id;
    },
    deleteExpenseItem(deleteExpenseId) {
      this.$emit("deleteExpenseItem", deleteExpenseId);
      this.deleteExpenseModalIsOpen = false;
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
    },
    sort(s) {
      if (s === this.currentSort) {
        this.currentSortDir = this.currentSortDir === "asc" ? "desc" : "asc";
      }
      this.currentSort = s;
    },
    returnUrl(id) {
      return "/expenses/" + id.split("-").join("") + "/update";
    }
  },
  mounted: function() {},
  computed: {
    sortedExpenses: function() {
      if (this.convertedExpenses.length > 0) {
        return _.orderBy(
          this.convertedExpenses,
          [this.currentSort],
          [this.currentSortDir]
        );
      }
      return _.orderBy(
        this.expenses,
        [this.currentSort],
        [this.currentSortDir]
      );
    }
  }
};
</script>

<style></style>
