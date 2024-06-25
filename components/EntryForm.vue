<script setup lang="ts">
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "~/components/ui/card";
import { Input } from "~/components/ui/input";
import { Label } from "~/components/ui/label";
import {
  HoverCard,
  HoverCardContent,
  HoverCardTrigger,
} from "~/components/ui/hover-card";
import { Icon } from "@iconify/vue";
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from "@/Components/ui/dropdown-menu";
import { Switch } from "~/components/ui/switch";
import { RadioGroup, RadioGroupItem } from "@/Components/ui/radio-group";
import { defineEmits } from "vue";

const emit = defineEmits(["remove", "entriesUpdated"]);

const props = defineProps<{
  entry: Object;
}>();

const fields = [
  {
    title: "Host",
    description:
      "An alias or name for the SSH configuration. This can be a single hostname or a wildcard (e.g., `*` for all hosts).",
    type: "text",
    required: true,
  },
  {
    title: "HostName",
    description:
      "The actual address of the server (either an IP address or domain name).",
    type: "text",
    required: true,
  },
  {
    title: "User",
    description: "The username used to establish the connection.",
    type: "text",
    required: true,
  },
  {
    title: "Port",
    description: "The port used for the connection (default is 22).",
    type: "number",
    required: false,
  },
  {
    title: "IdentityFile",
    description: "The path to the private key file used for authentication.",
    type: "text",
    required: false,
  },
  {
    title: "ForwardAgent",
    description: "Whether the SSH agent should be forwarded (yes/no).",
    type: "boolean",
    required: false,
  },
  {
    title: "ProxyJump",
    description:
      "One or more intermediate hosts through which the connection is made.",
    type: "text",
    required: false,
  },
  {
    title: "ServerAliveInterval",
    description:
      "Interval in seconds after which a null packet is sent to the server to keep the connection alive.",
    type: "number",
    required: false,
  },
  {
    title: "ServerAliveCountMax",
    description:
      "The number of server alive checks that can fail before the connection is terminated.",
    type: "number",
    required: false,
  },
  {
    title: "Compression",
    description: "Whether data compression is enabled (yes/no).",
    type: "boolean",
    required: false,
  },
  {
    title: "StrictHostKeyChecking",
    description: "Settings for host key verification (yes/no/ask).",
    type: "option",
    options: ["yes", "no", "ask"],
    required: false,
  },
  {
    title: "UserKnownHostsFile",
    description: "The path to the file containing known host keys.",
    type: "text",
    required: false,
  },
  {
    title: "LogLevel",
    description:
      "The desired level of logging (e.g., QUIET, FATAL, ERROR, INFO, VERBOSE, DEBUG, DEBUG1, DEBUG2, DEBUG3).",
    options: [
      "QUIET",
      "FATAL",
      "ERROR",
      "INFO",
      "VERBOSE",
      "DEBUG",
      "DEBUG1",
      "DEBUG2",
      "DEBUG3",
    ],
    type: "option",
    required: false,
  },
  {
    title: "ControlMaster",
    description: "Whether connection sharing is enabled (yes/no/auto).",
    type: "text",
    required: false,
  },
  {
    title: "ControlPath",
    description: "The path to the control socket for connection sharing.",
    type: "text",
    required: false,
  },
  {
    title: "ControlPersist",
    description:
      "Whether and how long SSH connections should persist after the last process has terminated. This can be specified as a duration (e.g., '5m') or in seconds (number).",
    type: "text",
    required: false,
  },
];

const form = ref({
  Host: "",
  HostName: "",
  User: "",
});

const visibleFields = computed(() => {
  return fields.filter((field) => field.title in form.value);
});

const availableFields = computed(() => {
  return fields.filter((field) => {
    return !(field.title in form.value);
  });
});

const addField = (field: string) => {
  if (!(field in form.value)) {
    form.value[field] = null;
  }
};

// watch form and emit the form on change to parent
watch(
  form,
  () => {
    emit("entriesUpdated", form.value);
  },
  { deep: true },
);

const removeField = (field: string) => {
  delete form.value[field];
};
</script>

<template>
  <Card>
    <CardHeader>
      <div class="flex items-center justify-between">
        <CardTitle>New Entry</CardTitle>
        <CardDescription>
          <Button variant="destructive" type="button" @click="$emit('remove')">
            <Icon
              icon="radix-icons:trash"
              class="h-[1.2rem] w-[1.2rem] text-white dark:text-foreground"
            />
          </Button>
        </CardDescription>
      </div>
    </CardHeader>
    <CardContent class="flex flex-col gap-6" v-auto-animate>
      <div v-for="(field, idx) in visibleFields" :key="idx" class="space-y-2">
        <div class="flex items-center justify-between">
          <Label :for="field.title">{{ field.title }}</Label>
          <HoverCard>
            <HoverCardTrigger>
              <Icon
                icon="radix-icons:info-circled"
                class="h-[1.2rem] w-[1.2rem] text-foreground"
              />
            </HoverCardTrigger>
            <HoverCardContent>
              {{ field.description }}
            </HoverCardContent>
          </HoverCard>
        </div>
        <div class="flex items-center gap-2 justify-between">
          <Input
            v-if="field.type === 'text'"
            v-model="form[field.title]"
            type="text"
            :id="field.title"
            :required="field.required"
          />
          <Switch
            v-else-if="field.type === 'boolean'"
            v-model="form[field.title]"
            :id="field.title"
          />
          <Input
            v-else-if="field.type === 'number'"
            v-model="form[field.title]"
            type="number"
            :id="field.title"
          />
          <RadioGroup
            v-else-if="field.type === 'option'"
            class="space-y-1"
            v-model="form[field.title]"
          >
            <div
              class="flex items-center space-x-2"
              v-for="(option, optionIdx) in field.options"
              :key="optionIdx"
            >
              <RadioGroupItem :id="option" :value="option" />
              <Label :for="option">{{ option }}</Label>
            </div>
          </RadioGroup>
          <Button
            variant="destructive"
            type="button"
            @click="removeField(field.title)"
          >
            <Icon
              icon="radix-icons:trash"
              class="h-[1.2rem] w-[1.2rem] text-white dark:text-foreground"
            />
          </Button>
        </div>
      </div>
    </CardContent>
    <CardFooter>
      <DropdownMenu class="w-full">
        <DropdownMenuTrigger class="w-full">
          <Button class="w-full">Add field</Button>
        </DropdownMenuTrigger>
        <DropdownMenuContent class="w-full">
          <DropdownMenuLabel>Available Fields</DropdownMenuLabel>
          <DropdownMenuSeparator />
          <DropdownMenuItem
            v-for="(availableField, idx) in availableFields"
            :key="idx"
            @click="addField(availableField.title)"
            >{{ availableField.title }}
          </DropdownMenuItem>
        </DropdownMenuContent>
      </DropdownMenu>
    </CardFooter>
  </Card>
</template>

<style scoped></style>
