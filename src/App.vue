<template>
  <div class="p-4">
    <div class="flex gap-2">
      <h1 class="text-2xl font-bold mb-4">Pusher ADE</h1>
      <a href="https://www.github.com/photomz/pusher-ade" target="_blank">
        <GithubLogoIcon class="w-8 h-8 p-1 text-gray-600" />
      </a>
    </div>
    <div class="flex gap-2">
      <div class="flex flex-col gap-2">
        <Input placeholder="Channel Name" v-model="channelId" />
        <div class="flex flex-row gap-1">
          <Badge
            variant="secondary"
            @click="channelId = 'your-default-channel-id'"
            >Default</Badge
          >
          <Badge
            variant="secondary"
            @click="channelId = 'your-public-channel-id'"
            >Public</Badge
          >
        </div>
      </div>
      <Input placeholder="Events (comma-sep)" v-model="events" />
      <RefreshIcon
        class="min-w-10 h-10 p-2.5 border border-gray-300 bg-gray-100 rounded-md"
        @click="clear"
      />
      <Button v-if="status == 'disconnected'" @click="connect">Connect</Button>
      <Button v-else class="bg-green-500" @click="exit">Connected</Button>
    </div>
    <div
      class="flex flex-row gap-4 align-start mt-8"
      v-for="(log, index) in eventsLog"
      :key="index"
    >
      <p class="text-sm text-gray-500 font-mono">
        {{ log.event }} - {{ log.timestamp.toLocaleTimeString() }}
      </p>
      <code v-html="log.data" class="language-json text-wrap"></code>
    </div>
    <!-- </div> -->
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, nextTick, watch } from "vue";
import { Input } from "@/components/ui/input";
import { Badge } from "@/components/ui/badge";
import { Button } from "@/components/ui/button";
import Pusher from "pusher-js";
import { highlight, languages } from "prismjs";
import { SymbolIcon as RefreshIcon, GithubLogoIcon } from "@radix-icons/vue";
import "prismjs/components/prism-json";

// Environment variables
const pusherKey = import.meta.env.VITE_PUSHER_KEY;
const pusherCluster = import.meta.env.VITE_PUSHER_CLUSTER;

const pusher = new Pusher(pusherKey, {
  cluster: pusherCluster,
});

const status = ref("disconnected");
const channel = ref(null);
const channelId = ref(localStorage.getItem("channelId") || "");
const events = ref(localStorage.getItem("events") || "PING,STREAM_CHAT");
const eventsLog = reactive<{ event: string; timestamp: Date; data: string }[]>(
  []
);

watch(
  [channelId, events, eventsLog],
  () => {
    localStorage.setItem("channelId", channelId.value);
    localStorage.setItem("events", events.value);
  },
  { deep: true }
);

function onEvent(event: string, data: object) {
  // Format and highlight the JSON data
  const formattedData = JSON.stringify(data, null, 2);
  const highlightedData = highlight(formattedData, languages.json, "json");

  // Add the new event to the events log
  eventsLog.push({
    event,
    timestamp: new Date(),
    data: highlightedData,
  });

  // Scroll to the bottom of the events log container
  nextTick(() => {
    const scrollContainer = document.querySelector(".scrollwindow");
    if (scrollContainer) {
      scrollContainer.scrollTop = scrollContainer.scrollHeight;
    }
  });
}

function connect() {
  console.log("Connecting to Pusher");
  // Enable pusher logging - don't include this in production
  Pusher.logToConsole = true;

  channel.value = pusher.subscribe(channelId.value);
  events.value.split(",").forEach((event) => {
    channel.value.bind(event, (data: any) => {
      onEvent(event, data);
    });
  });
  status.value = "connected";
}

function clear() {
  eventsLog.splice(0, eventsLog.length);
}

function exit() {
  console.log("Disconnecting from Pusher");
  channel.value.unbind();
  status.value = "disconnected";
  // reset eventsLog
  clear();
}
</script>

<style scoped>
@import "https://cdnjs.cloudflare.com/ajax/libs/prism/1.25.0/themes/prism.min.css";
</style>
