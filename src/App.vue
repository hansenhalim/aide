<template>
  <div
    class="mx-auto flex min-h-screen max-w-7xl flex-col items-center justify-center sm:px-6 lg:px-8"
  >
    <div class="overflow-hidden bg-white shadow sm:rounded-lg">
      <div class="px-4 py-5 sm:p-6">
        <div class="px-4 py-5 sm:p-6">
          <h3 class="text-lg font-medium leading-6 text-gray-900">
            Create new schedule
          </h3>
          <div class="mt-2 max-w-xl text-sm text-gray-500">
            <p>Efficiently organize your time and tasks with a fresh agenda.</p>
          </div>
          <form
            class="mt-5 sm:flex sm:items-center"
            @submit.prevent="addSchedule"
          >
            <div class="w-full sm:max-w-xs">
              <input
                v-model="newSchedule.dispatched_at"
                step="1"
                type="datetime-local"
                class="block w-full rounded-md border-gray-300 shadow-sm focus:border-red-500 focus:ring-red-500 sm:text-sm"
              />
            </div>
            <div class="mt-2 w-full sm:ml-2 sm:mt-0 sm:max-w-xs">
              <input
                v-model="newSchedule.name"
                type="text"
                class="block w-full rounded-md border-gray-300 shadow-sm focus:border-red-500 focus:ring-red-500 sm:text-sm"
                placeholder="Schedule #1"
              />
            </div>
            <button
              type="submit"
              class="mt-3 inline-flex w-full items-center justify-center rounded-md border border-transparent bg-red-600 px-4 py-2 font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 sm:ml-3 sm:mt-0 sm:w-auto sm:text-sm"
            >
              Save
            </button>
          </form>
        </div>
        <div class="relative">
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
        <div class="mt-4 flow-root">
          <ul class="-mb-8">
            <li
              v-for="(schedule, scheduleIdx) in schedules.sort(
                (a, b) => a.dispatched_at - b.dispatched_at
              )"
              :key="schedule.uuid"
            >
              <div class="relative pb-8">
                <span
                  v-if="scheduleIdx !== schedules.length - 1"
                  class="absolute left-4 top-4 -ml-px h-full w-0.5 bg-gray-200"
                />
                <div class="relative flex space-x-3">
                  <a href="#" @click="deleteSchedule(schedule, scheduleIdx)">
                    <span
                      :class="[
                        schedule.iconBackground,
                        'flex h-8 w-8 items-center justify-center rounded-full ring-8 ring-white',
                      ]"
                    >
                      <component
                        :is="schedule.icon"
                        class="h-5 w-5 text-white"
                      />
                    </span>
                  </a>
                  <div
                    class="flex min-w-0 flex-1 justify-between space-x-4 pt-1.5"
                  >
                    <div>
                      <p class="text-sm text-gray-500">
                        {{ schedule.content }}
                        <a
                          @click="schedule.open = true"
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
                          ? schedule.dispatched_at.diff(now, "m") < 45
                            ? schedule.dispatched_at.from(now)
                            : schedule.dispatched_at.calendar(now)
                          : schedule.dispatched_at.format(niceFormat)
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
                                {{ schedule.content }}
                                {{ schedule.name }}
                              </DialogTitle>
                              <div class="mt-2">
                                <form
                                  class="mt-5 sm:flex sm:items-center"
                                  @submit.prevent="addSchedule"
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
                            class="mt-3 inline-flex w-full justify-center rounded-md border border-gray-300 bg-white px-4 py-2 text-base font-medium text-gray-700 shadow-sm hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 sm:ml-3 sm:mt-0 sm:w-auto sm:text-sm"
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

    <div class="mt-4 rounded-lg bg-white p-4 shadow">
      <span class="mt-2 font-mono text-4xl font-bold">
        {{ now.format(niceFormat) }}
      </span>
    </div>
  </div>
</template>

<script setup>
import { CheckIcon, TrashIcon } from "@heroicons/vue/24/solid";
import { ExclamationTriangleIcon } from "@heroicons/vue/24/outline";
import { ref, onMounted, onUnmounted } from "vue";
import dayjs from "dayjs";
import relativeTime from "dayjs/plugin/relativeTime";
import calendar from "dayjs/plugin/calendar";
import LocalizedFormat from "dayjs/plugin/LocalizedFormat";
import {
  Dialog,
  DialogOverlay,
  DialogTitle,
  TransitionChild,
  TransitionRoot,
} from "@headlessui/vue";

dayjs.extend(relativeTime);
dayjs.extend(calendar);
dayjs.extend(LocalizedFormat);

const niceFormat = "M/D/YY, h:mm:ss A";

const isRelative = ref(false);
const now = ref(dayjs());

const syncTime = () => {
  now.value = dayjs();
};

const newSchedule = ref({
  name: "Schedule #3",
  dispatched_at: dayjs().add(8, "h").startOf("h").toISOString().slice(0, -5),
});

const schedules = ref([
  {
    uuid: "51c4932d-d5dc-40a7-a517-90000c8c9bb6",
    icon: CheckIcon,
    iconBackground: "bg-green-500",
    content: "Dispatch",
    name: "Schedule #1",
    dispatched_at: dayjs("2023-06-18T09:00:00"),
    fetch_url: "http://10.10.0.107:8088/api/?Function=Fade",
    open: false,
  },
  {
    uuid: "2207d549-a9e7-45e5-856c-bb3d729d5252",
    icon: CheckIcon,
    iconBackground: "bg-green-500",
    content: "Dispatch",
    name: "Schedule #2",
    dispatched_at: dayjs("2023-06-18T09:00:00"),
    fetch_url: "http://10.10.0.107:8088/api/?Function=Fade",
    open: false,
  },
]);

const dispatchSchedule = (schedule) => {
  fetch(schedule.fetch_url);
};

const addSchedule = () => {
  const newScheduleObj = {
    uuid: crypto.randomUUID(),
    icon: CheckIcon,
    iconBackground: "bg-green-500",
    content: "Dispatch",
    name: newSchedule.value.name,
    dispatched_at: dayjs(newSchedule.value.dispatched_at),
    fetch_url: "http://10.10.0.107:8088/api/?Function=Fade",
    open: false,
  };
  schedules.value.push(newScheduleObj);
};

const deleteSchedule = (schedule, index) => {
  if (schedule.isConfirmDeletion) {
    schedules.value.splice(index, 1);
  } else {
    schedule.isConfirmDeletion = true;
    schedule.iconBackground = "bg-red-500";
    schedule.icon = TrashIcon;
  }
};

let syncInterval;
let toggleTimeout;

const toggleIsRelative = () => {
  isRelative.value = !isRelative.value;
  clearTimeout(toggleTimeout);
};

onMounted(() => {
  syncInterval = setInterval(syncTime, 500);
  toggleTimeout = setTimeout(() => (isRelative.value = true), 3000);
});

onUnmounted(() => {
  clearInterval(syncInterval);
});
</script>
