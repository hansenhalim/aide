<template>
  <div class="bg-white overflow-hidden shadow sm:rounded-lg">
    <div class="px-4 py-5 sm:p-6">
      <div class="px-4 py-5 sm:p-6">
        <h3 class="text-lg leading-6 font-medium text-gray-900">
          Create new schedule
        </h3>
        <div class="mt-2 max-w-xl text-sm text-gray-500">
          <p>Efficiently organize your time and tasks with a fresh agenda.</p>
        </div>
        <form
          class="mt-5 sm:flex sm:items-center"
          @submit.prevent="
            schedules.push({
              uuid: randomUUID(),
              icon: CheckIcon,
              iconBackground: 'bg-green-500',
              content: 'Dispatch',
              name: name,
              dispatched_at: new Date(dispatched_at),
            })
          "
        >
          <div class="w-full sm:max-w-xs">
            <input
              v-model="dispatched_at"
              step="1"
              type="datetime-local"
              class="shadow-sm focus:ring-indigo-500 focus:border-indigo-500 block w-full sm:text-sm border-gray-300 rounded-md"
            />
          </div>
          <div class="w-full sm:max-w-xs ml-2">
            <input
              v-model="name"
              type="text"
              class="shadow-sm focus:ring-indigo-500 focus:border-indigo-500 block w-full sm:text-sm border-gray-300 rounded-md"
              placeholder="Schedule #1"
            />
          </div>
          <button
            type="submit"
            class="mt-3 w-full inline-flex items-center justify-center px-4 py-2 border border-transparent shadow-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm"
          >
            Save
          </button>
        </form>
      </div>
      <div class="relative">
        <div class="absolute inset-0 flex items-center" aria-hidden="true">
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
      <div class="flow-root mt-4">
        <ul role="list" class="-mb-8">
          <li
            v-for="(schedule, scheduleIdx) in schedules.slice(0, 5)"
            :key="schedule.uuid"
          >
            <div class="relative pb-8">
              <span
                v-if="scheduleIdx !== schedules.length - 1"
                class="absolute top-4 left-4 -ml-px h-full w-0.5 bg-gray-200"
              />
              <div class="relative flex space-x-3">
                <div>
                  <span
                    :class="[
                      schedule.iconBackground,
                      'h-8 w-8 rounded-full flex items-center justify-center ring-8 ring-white',
                    ]"
                  >
                    <component :is="schedule.icon" class="h-5 w-5 text-white" />
                  </span>
                </div>
                <div
                  class="min-w-0 flex-1 pt-1.5 flex justify-between space-x-4"
                >
                  <div>
                    <p class="text-sm text-gray-500">
                      {{ schedule.content }}
                      <a
                        href="#"
                        @click="() => schedules.splice(scheduleIdx, 1)"
                        class="font-medium text-gray-900"
                        >{{ schedule.name }}</a
                      >
                    </p>
                  </div>
                  <div
                    class="text-right text-sm whitespace-nowrap text-gray-500"
                  >
                    {{ schedule.dispatched_at.toLocaleString() }}
                  </div>
                </div>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>

  <div class="bg-white overflow-hidden shadow sm:rounded-lg mt-4 hidden">
    <div class="text-red">Server: {{ server }}</div>
    <div>Client: {{ client }}</div>
    <button
      class="mt-3 w-full inline-flex items-center justify-center px-4 py-2 border border-transparent shadow-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm"
      @click="fetchTime"
    >
      fetchTime
    </button>

    <table class="table-auto mt-4 max-w-xl">
      <thead>
        <tr>
          <th class="border">UUID</th>
          <th class="border">Icon</th>
          <th class="border">Icon Background</th>
          <th class="border">Content</th>
          <th class="border">Name</th>
          <th class="border">Dispatched At</th>
        </tr>
      </thead>
      <tbody class="text-left">
        <tr v-for="schedule in schedules" :key="schedule.uuid">
          <td class="border">{{ schedule.uuid }}</td>
          <td class="border">{{ schedule.icon }}</td>
          <td class="border">{{ schedule.iconBackground }}</td>
          <td class="border">{{ schedule.content }}</td>
          <td class="border">{{ schedule.name }}</td>
          <td class="border">{{ schedule.dispatched_at }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { CheckIcon, HandThumbUpIcon, UserIcon } from "@heroicons/vue/24/solid";
import { ref } from "vue";

const server = ref();
const client = ref();

const schedules = ref([
  {
    uuid: "51c4932d-d5dc-40a7-a517-90000c8c9bb6",
    icon: UserIcon,
    iconBackground: "bg-gray-400",
    content: "Applied to",
    name: "Build an app",
    dispatched_at: new Date("2023-06-18T09:00:00"),
  },
  {
    uuid: "e2379b6f-1b54-4325-a348-e476b2e8ffed",
    icon: HandThumbUpIcon,
    iconBackground: "bg-blue-500",
    content: "Advanced to phone screening by",
    name: "Eat ice cream",
    dispatched_at: new Date("2023-06-18T09:05:00"),
  },
  {
    uuid: "7869c24e-d226-4430-9ab6-9d3330633126",
    icon: CheckIcon,
    iconBackground: "bg-green-500",
    content: "Completed phone screening with",
    name: "Learn how to code",
    dispatched_at: new Date("2023-06-18T09:10:00"),
  },
  {
    uuid: "d7eebbfc-ae3d-4d34-9e71-d99307d4c62c",
    icon: HandThumbUpIcon,
    iconBackground: "bg-blue-500",
    content: "Advanced to interview by",
    name: "Go to the gym",
    dispatched_at: new Date("2023-06-18T09:15:00"),
  },
  {
    uuid: "ce7765cd-33ec-4a62-a8be-739f1d478ec3",
    icon: CheckIcon,
    iconBackground: "bg-green-500",
    content: "Completed interview with",
    name: "Sleep well",
    dispatched_at: new Date("2023-06-18T09:30:00"),
  },
]);

const name = ref("Schedule #1");
const dispatched_at = ref("2023-06-18T09:15:00");

function fetchTime() {
  const options = { method: "GET", headers: { Accept: "application/json" } };

  fetch("http://worldtimeapi.org/api/timezone/Asia/Jakarta", options)
    .then((response) => response.json())
    .then((response) => {
      server.value = new Date(response.datetime);
      client.value = new Date();
    })
    .catch((err) => console.error(err));
}

function randomUUID() {
  return crypto.randomUUID();
}
</script>
