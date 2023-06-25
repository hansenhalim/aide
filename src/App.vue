<template>
  <div
    class="mx-auto flex min-h-screen max-w-7xl flex-col items-center justify-center sm:px-6 lg:px-8"
  >
    <div class="overflow-hidden bg-white shadow sm:rounded-lg">
      <div class="px-4 py-5 sm:p-6">
        <!-- assistance -->
        <SwitchGroup
          as="div"
          class="flex items-center justify-between"
          @click="recalculateTimeout()"
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
                      'flex h-8 w-8 items-center justify-center rounded-full ring-8 ring-white',
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
                          @click="
                            schedule.open = true;
                            disableAssistance();
                          "
                          href="#"
                          class="font-medium text-gray-900"
                        >
                          {{ schedule.name }}
                        </a>
                      </p>
                    </div>
                    <a
                      href="#"
                      @click="toggleIsRelative()"
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

              <TransitionRoot as="template" :show="schedule.open">
                <Dialog
                  as="div"
                  class="fixed inset-0 z-10 overflow-y-auto"
                  @close="schedule.open = false"
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
                              <div class="mt-2">
                                <form
                                  class="mt-5 sm:flex sm:items-center"
                                  @submit.prevent="dispatchSchedule(schedule)"
                                >
                                  <div class="w-full sm:max-w-xs">
                                    <input
                                      v-model="schedule.dispatched_at"
                                      step="1"
                                      type="datetime-local"
                                      class="block w-full rounded-md border-gray-300 shadow-sm focus:border-red-500 focus:ring-red-500 sm:text-sm"
                                    />
                                  </div>
                                  <div
                                    class="mt-2 w-full sm:ml-2 sm:mt-0 sm:max-w-xs"
                                  >
                                    <input
                                      v-model="schedule.name"
                                      type="text"
                                      class="block w-full rounded-md border-gray-300 shadow-sm focus:border-red-500 focus:ring-red-500 sm:text-sm"
                                      placeholder="Schedule #1"
                                    />
                                  </div>
                                </form>
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
                            @click="schedule.open = false"
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
  </div>
</template>

<script setup>
import { ExclamationTriangleIcon, ClockIcon } from "@heroicons/vue/24/outline";
import { ref, onMounted, onUnmounted } from "vue";
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

dayjs.extend(relativeTime);
dayjs.extend(calendar);
dayjs.extend(LocalizedFormat);

var syncInterval;
const goodTemplate = "M/D/YY, h:mm:ss A";

const now = ref(dayjs());
const isRelative = ref(false);
const isAssistanceEnabled = ref(false);

const recalculateTimeout = () => {
  schedules.value.forEach((schedule) => {
    const timeout = dayjs(schedule.dispatched_at).diff(now.value);

    clearTimeout(schedule.timeout);
    delete schedule.timeout;

    if (isAssistanceEnabled.value && timeout > 0) {
      schedule.timeout = setTimeout(() => {
        dispatchSchedule(schedule);
      }, timeout);
    }
  });
};

const schedules = ref([
  {
    uuid: "756bdf7d-756c-4e2c-a42e-4a73979d19c4",
    name: "Fade to source #2",
    dispatched_at: "2023-06-25T08:43:00",
    fetch_url: "http://vmix.local:8088/api/?Function=Fade",
    open: false,
  },
  {
    uuid: "fd888e96-9332-4911-9662-7565f22cc14f",
    name: "Go live",
    dispatched_at: "2023-06-25T08:45:00",
    fetch_url: null,
    open: false,
  },
  {
    uuid: "1beb2f5b-b35e-4141-9316-c080fc66f307",
    name: "Fade to source #1",
    dispatched_at: "2023-06-25T08:51:03",
    fetch_url: "http://vmix.local:8088/api/?Function=Fade",
    open: false,
  },
  {
    uuid: "34b62ba0-5631-40af-a984-598cc6a14ffa",
    name: "Fade to source #2",
    dispatched_at: "2023-06-25T10:43:00",
    fetch_url: "http://vmix.local:8088/api/?Function=Fade",
    open: false,
  },
  {
    uuid: "4dba120a-6b06-438d-a754-35b94e05d296",
    name: "Go live",
    dispatched_at: "2023-06-25T10:45:00",
    fetch_url: null,
    open: false,
  },
  {
    uuid: "d51772ef-2017-42c1-a8aa-cba897bf4b07",
    name: "Fade to source #1",
    dispatched_at: "2023-06-25T10:51:03",
    fetch_url: "http://vmix.local:8088/api/?Function=Fade",
    open: false,
  },
  {
    uuid: "6176ff63-1a8a-4a70-9f3e-17f7ba204abb",
    name: "Fade to source #2",
    dispatched_at: "2023-06-25T12:43:00",
    fetch_url: "http://vmix.local:8088/api/?Function=Fade",
    open: false,
  },
  {
    uuid: "73bd9811-5a01-453a-b94e-9e0d4987f79d",
    name: "Go live",
    dispatched_at: "2023-06-25T12:45:00",
    fetch_url: null,
    open: false,
  },
  {
    uuid: "9361f8fd-8ed9-4f2a-a1f2-895ee50134cb",
    name: "Fade to source #1",
    dispatched_at: "2023-06-25T12:51:03",
    fetch_url: "http://vmix.local:8088/api/?Function=Fade",
    open: false,
  },
  {
    uuid: "72d93b62-3c21-465e-9372-fca07e8fa484",
    name: "Fade to source #2",
    dispatched_at: "2023-06-25T14:43:00",
    fetch_url: "http://vmix.local:8088/api/?Function=Fade",
    open: false,
  },
  {
    uuid: "845a73d7-8488-4bf5-a4e1-5320d75e03ca",
    name: "Go live",
    dispatched_at: "2023-06-25T14:45:00",
    fetch_url: null,
    open: false,
  },
  {
    uuid: "947a5d35-ff8b-42bd-9b5d-73010e0e0b25",
    name: "Fade to source #1",
    dispatched_at: "2023-06-25T14:51:03",
    fetch_url: "http://vmix.local:8088/api/?Function=Fade",
    open: false,
  },
  {
    uuid: "9be641cd-de94-4f52-8418-a521668d0471",
    name: "Fade to source #2",
    dispatched_at: "2023-06-25T16:43:00",
    fetch_url: "http://vmix.local:8088/api/?Function=Fade",
    open: false,
  },
  {
    uuid: "cc389b97-3d92-4d29-894f-982c8da7af4a",
    name: "Go live",
    dispatched_at: "2023-06-25T16:45:00",
    fetch_url: null,
    open: false,
  },
  {
    uuid: "4c6ecb74-20e6-4dcd-838c-e4dc76ab2a5c",
    name: "Fade to source #1",
    dispatched_at: "2023-06-25T16:51:03",
    fetch_url: "http://vmix.local:8088/api/?Function=Fade",
    open: false,
  },
  {
    uuid: "104d6aa7-7f51-4e22-bfd8-530933330624",
    name: "Fade to source #2",
    dispatched_at: "2023-06-25T18:43:00",
    fetch_url: "http://vmix.local:8088/api/?Function=Fade",
    open: false,
  },
  {
    uuid: "f4ec9f99-665f-4b98-a11e-0d8dc14e4535",
    name: "Go live",
    dispatched_at: "2023-06-25T18:45:00",
    fetch_url: null,
    open: false,
  },
  {
    uuid: "eaa19abb-7416-426b-8c24-553096d8ff85",
    name: "Fade to source #1",
    dispatched_at: "2023-06-25T18:51:03",
    fetch_url: "http://vmix.local:8088/api/?Function=Fade",
    open: false,
  },
]);

const dispatchSchedule = (schedule) => {
  console.log(`Dispatching ${schedule.name}`);

  if (schedule.fetch_url) {
    fetch(schedule.fetch_url);
    console.log(`Dispatched ${schedule.name}`);
  }
};

const disableAssistance = () => (isAssistanceEnabled.value = false);

const toggleIsRelative = () => {
  isRelative.value = !isRelative.value;
};

onMounted(() => {
  syncInterval = setInterval(() => (now.value = dayjs()), 500);
  setTimeout(() => (isRelative.value = true), 5000);
});

onUnmounted(() => {
  clearInterval(syncInterval);
});
</script>
