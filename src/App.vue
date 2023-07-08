<template>
  <div
    class="mx-auto flex min-h-screen max-w-7xl flex-col items-center justify-center sm:px-6 lg:px-8"
  >
    <div class="w-full max-w-lg overflow-hidden bg-white shadow sm:rounded-lg">
      <div class="px-4 py-5 sm:p-6">
        <!-- assistance -->
        <SwitchGroup
          as="div"
          class="flex items-center justify-between"
          @click="recalculateTimeout"
        >
          <span class="flex flex-grow flex-col">
            <SwitchLabel
              as="span"
              class="text-sm font-medium text-gray-900"
              passive
              >Enable assistance</SwitchLabel
            >
            <SwitchDescription as="span" class="text-sm text-gray-500"
              >Effortless Support at Your Fingertips.</SwitchDescription
            >
          </span>
          <Switch
            v-model="isAssistanceEnabled"
            :class="[
              isAssistanceEnabled ? 'bg-red-600' : 'bg-gray-200',
              'relative inline-flex h-6 w-11 flex-shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors duration-200 ease-in-out focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2',
            ]"
          >
            <span
              :class="[
                isAssistanceEnabled ? 'translate-x-5' : 'translate-x-0',
                'pointer-events-none relative inline-block h-5 w-5 transform rounded-full bg-white shadow ring-0 transition duration-200 ease-in-out',
              ]"
            >
              <span
                :class="[
                  isAssistanceEnabled
                    ? 'opacity-0 duration-100 ease-out'
                    : 'opacity-100 duration-200 ease-in',
                  'absolute inset-0 flex h-full w-full items-center justify-center transition-opacity',
                ]"
              >
                <svg
                  class="h-3 w-3 text-gray-400"
                  fill="none"
                  viewBox="0 0 12 12"
                >
                  <path
                    d="M4 8l2-2m0 0l2-2M6 6L4 4m2 2l2 2"
                    stroke="currentColor"
                    stroke-width="2"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                  />
                </svg>
              </span>
              <span
                :class="[
                  isAssistanceEnabled
                    ? 'opacity-100 duration-200 ease-in'
                    : 'opacity-0 duration-100 ease-out',
                  'absolute inset-0 flex h-full w-full items-center justify-center transition-opacity',
                ]"
              >
                <svg
                  class="h-3 w-3 text-red-600"
                  fill="currentColor"
                  viewBox="0 0 12 12"
                >
                  <path
                    d="M3.707 5.293a1 1 0 00-1.414 1.414l1.414-1.414zM5 8l-.707.707a1 1 0 001.414 0L5 8zm4.707-3.293a1 1 0 00-1.414-1.414l1.414 1.414zm-7.414 2l2 2 1.414-1.414-2-2-1.414 1.414zm3.414 2l4-4-1.414-1.414-4 4 1.414 1.414z"
                  />
                </svg>
              </span>
            </span>
          </Switch>
        </SwitchGroup>

        <!-- + divider + -->
        <div class="relative mt-4">
          <div class="absolute inset-0 flex items-center">
            <div class="w-full border-t border-gray-300" />
          </div>
          <div class="relative flex justify-center">
            <span class="bg-white px-2 text-gray-500">
              <svg
                class="h-5 w-5 text-gray-500"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 20 20"
              >
                <path
                  fill="#6B7280"
                  fill-rule="evenodd"
                  d="M10 3a1 1 0 011 1v5h5a1 1 0 110 2h-5v5a1 1 0 11-2 0v-5H4a1 1 0 110-2h5V4a1 1 0 011-1z"
                  clip-rule="evenodd"
                />
              </svg>
            </span>
          </div>
        </div>

        <!-- timeline -->
        <div class="mt-4 flow-root max-h-56 overflow-y-scroll">
          <ul class="-mb-8">
            <li
              v-for="(schedule, scheduleIdx) in schedules"
              :key="schedule.uuid"
              ref="scheduleRefs"
            >
              <div class="relative pb-8">
                <span
                  v-if="scheduleIdx !== schedules.length - 1"
                  class="absolute left-4 top-4 -ml-px h-full w-0.5 bg-gray-200"
                />
                <div class="relative flex space-x-3">
                  <span
                    :class="[
                      schedule.timeout ? 'bg-red-500' : 'bg-gray-500',
                      'flex h-8 w-8 items-center justify-center rounded-full ring-8 ring-white transition',
                    ]"
                  >
                    <ClockIcon class="h-5 w-5 text-white" />
                  </span>
                  <div
                    class="flex min-w-0 flex-1 justify-between space-x-4 pt-1.5"
                  >
                    <div>
                      <p class="text-sm text-gray-500">
                        {{ schedule.dispatched_at.slice(11) }}
                        <a
                          @click="schedule.isModalShown = true"
                          href="#"
                          class="font-medium text-gray-900"
                        >
                          {{ schedule.name }}
                        </a>
                      </p>
                    </div>
                    <a
                      href="#"
                      @click="() => (isRelative = !isRelative)"
                      class="whitespace-nowrap text-right text-sm text-gray-500"
                    >
                      {{
                        isRelative
                          ? dayjs(schedule.dispatched_at).diff(now, "m") < 45
                            ? dayjs(schedule.dispatched_at).from(now)
                            : dayjs(schedule.dispatched_at).calendar(now)
                          : dayjs(schedule.dispatched_at).format(goodTemplate)
                      }}
                    </a>
                  </div>
                </div>
              </div>

              <TransitionRoot as="template" :show="schedule.isModalShown">
                <Dialog
                  as="div"
                  class="fixed inset-0 z-10 overflow-y-auto"
                  @close="schedule.isModalShown = false"
                >
                  <div
                    class="flex min-h-screen items-end justify-center px-4 pb-20 pt-4 text-center sm:block sm:p-0"
                  >
                    <TransitionChild
                      as="template"
                      enter="ease-out duration-300"
                      enter-from="opacity-0"
                      enter-to="opacity-100"
                      leave="ease-in duration-200"
                      leave-from="opacity-100"
                      leave-to="opacity-0"
                    >
                      <DialogOverlay
                        class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity"
                      />
                    </TransitionChild>

                    <!-- This element is to trick the browser into centering the modal contents. -->
                    <span
                      class="hidden sm:inline-block sm:h-screen sm:align-middle"
                      >&#8203;</span
                    >
                    <TransitionChild
                      as="template"
                      enter="ease-out duration-300"
                      enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
                      enter-to="opacity-100 translate-y-0 sm:scale-100"
                      leave="ease-in duration-200"
                      leave-from="opacity-100 translate-y-0 sm:scale-100"
                      leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
                    >
                      <div
                        class="relative inline-block transform overflow-hidden rounded-lg bg-white text-left align-bottom shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-lg sm:align-middle"
                      >
                        <div class="bg-white px-4 pb-4 pt-5 sm:p-6 sm:pb-4">
                          <div class="sm:flex sm:items-start">
                            <div
                              class="mx-auto flex h-12 w-12 flex-shrink-0 items-center justify-center rounded-full bg-red-100 sm:mx-0 sm:h-10 sm:w-10"
                            >
                              <ExclamationTriangleIcon
                                class="h-6 w-6 text-red-600"
                              />
                            </div>
                            <div
                              class="mt-3 text-center sm:ml-4 sm:mt-0 sm:text-left"
                            >
                              <DialogTitle
                                as="h3"
                                class="text-lg font-medium leading-6 text-gray-900"
                              >
                                {{ schedule.name }}
                              </DialogTitle>
                              <div class="mt-2 text-sm text-gray-500">
                                Lorem ipsum dolor sit amet consectetur
                                adipisicing elit. Optio ad quasi doloremque,
                                fugit aliquam temporibus voluptatum repellendus
                                officiis accusamus quae quo? Ut iure at, qui
                                vitae iusto illum quam harum?
                              </div>
                            </div>
                          </div>
                        </div>
                        <div
                          class="bg-gray-50 px-4 py-3 sm:flex sm:flex-row-reverse sm:px-6"
                        >
                          <button
                            type="button"
                            class="inline-flex w-full justify-center rounded-md border border-transparent bg-red-600 px-4 py-2 text-base font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 sm:ml-3 sm:w-auto sm:text-sm"
                            @click="dispatchSchedule(schedule)"
                          >
                            Dispatch
                          </button>
                          <button
                            type="button"
                            class="mt-3 inline-flex w-full justify-center rounded-md border border-gray-300 bg-white px-4 py-2 text-base font-medium text-gray-700 shadow-sm hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 sm:ml-3 sm:mt-0 sm:w-auto sm:text-sm"
                            @click="schedule.isModalShown = false"
                          >
                            Cancel
                          </button>
                        </div>
                      </div>
                    </TransitionChild>
                  </div>
                </Dialog>
              </TransitionRoot>
            </li>
          </ul>
        </div>
      </div>
    </div>

    <!-- clock -->
    <div class="mt-4 rounded-lg bg-white p-4 shadow">
      <span class="mt-2 font-mono text-4xl font-bold">
        {{ now.format(goodTemplate) }}
      </span>
    </div>

    <!-- google -->
    <div class="mt-4">
      <!-- 118289694952-idlqqjbiuagqm8laevkp8lrq3gm8emur.apps.googleusercontent.com -->
    </div>

    <!-- button -->
    <button
      @click="loadLivestreams"
      type="button"
      class="mt-4 inline-flex items-center rounded border border-transparent bg-indigo-600 px-2.5 py-1.5 text-xs font-medium text-white shadow-sm hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2"
    >
      loadLivestreams
    </button>
  </div>
</template>

<script setup>
import { ExclamationTriangleIcon, ClockIcon } from "@heroicons/vue/24/outline";
import { ref, onMounted } from "vue";
import dayjs from "dayjs";
import relativeTime from "dayjs/plugin/relativeTime";
import calendar from "dayjs/plugin/calendar";
import LocalizedFormat from "dayjs/plugin/LocalizedFormat";
import {
  Dialog,
  DialogOverlay,
  DialogTitle,
  Switch,
  SwitchDescription,
  SwitchGroup,
  SwitchLabel,
  TransitionChild,
  TransitionRoot,
} from "@headlessui/vue";
import { schedules } from "./schedules";

dayjs.extend(relativeTime);
dayjs.extend(calendar);
dayjs.extend(LocalizedFormat);

const goodTemplate = "M/D/YY, h:mm:ss A";

const now = ref(dayjs());
const isRelative = ref(false);
const isAssistanceEnabled = ref(false);

const recalculateTimeout = () => {
  schedules.value.forEach((schedule) => {
    const timeout = dayjs(schedule.dispatched_at).diff(now.value);

    clearTimeout(schedule.timeout);
    schedule.timeout = null;

    if (isAssistanceEnabled.value && timeout > 0) {
      schedule.timeout = setTimeout(() => {
        dispatchSchedule(schedule);
      }, timeout);
    }
  });

  if (isAssistanceEnabled.value) {
    scrollToNearestSchedule();
    document.title = "Assisting...";
  } else {
    document.title = "AIDE";
  }
};

const dispatchSchedule = (schedule) => {
  console.log(`Dispatching ${schedule.name}`);

  clearTimeout(schedule.timeout);
  schedule.timeout = null;

  if (schedule.fetch_url) {
    fetch(schedule.fetch_url);
    console.log(`Dispatched ${schedule.name}`);
  }

  scrollToNearestSchedule();
};

const scheduleRefs = ref([]);

const scrollToNearestSchedule = () => {
  const index = schedules.value.findIndex((schedule) => schedule.timeout);
  scheduleRefs.value[Math.max(index, 0)].scrollIntoView({
    behavior: "smooth",
  });
};

let tokenClient;

function gapiInit() {
  gapi.client.init({}).then(function () {
    gapi.client.load(
      "https://www.googleapis.com/discovery/v1/apis/youtube/v3/rest"
    );
  });
}

function gapiLoad() {
  gapi.load("client", gapiInit);
}

function gisInit() {
  tokenClient = google.accounts.oauth2.initTokenClient({
    client_id:
      "118289694952-idlqqjbiuagqm8laevkp8lrq3gm8emur.apps.googleusercontent.com",
    scope: "https://www.googleapis.com/auth/youtube",
  });
}

function loadLivestreams() {
  tokenClient.callback = (resp) => {
    if (resp.error !== undefined) {
      throw resp;
    }
    // GIS has automatically updated gapi.client with the newly issued access token.
    console.log(gapi.client.getToken());

    gapi.client.youtube.liveBroadcasts
      .list({
        part: ["snippet,contentDetails,status"],
        broadcastStatus: "upcoming",
      })
      .then((response) => console.log(response.result))
      .catch((err) => console.log(err));
  };

  // Conditionally ask users to select the Google Account they'd like to use,
  // and explicitly obtain their consent to fetch their Calendar.
  // NOTE: To request an access token a user gesture is necessary.
  if (gapi.client.getToken() === null) {
    // Prompt the user to select a Google Account and asked for consent to share their data
    // when establishing a new session.
    tokenClient.requestAccessToken({ prompt: "consent" });
  } else {
    // Skip display of account chooser and consent dialog for an existing session.
    tokenClient.requestAccessToken({ prompt: "" });
  }
}

onMounted(() => {
  setInterval(() => (now.value = dayjs()), 500);
  setTimeout(() => (isRelative.value = true), 5000);

  const apiScript = document.createElement("script");
  apiScript.src = "https://apis.google.com/js/api.js";
  apiScript.async = true;
  apiScript.defer = true;
  apiScript.onload = gapiLoad;
  document.body.appendChild(apiScript);

  const identityScript = document.createElement("script");
  identityScript.src = "https://accounts.google.com/gsi/client";
  identityScript.async = true;
  identityScript.defer = true;
  identityScript.onload = gisInit;
  document.body.appendChild(identityScript);
});
</script>
