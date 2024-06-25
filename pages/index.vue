<script setup lang="ts">
import EntryForm from "~/components/EntryForm.vue";
import {Icon} from "@iconify/vue";
import {toast} from 'vue-sonner'
import {Separator} from "~/components/ui/separator";

const entries = ref([])

const addEmptyEntry = () => {
  const entryTemplate = {
    Host: '',
    HostName: '',
    User: '',
  }

  entries.value.push(entryTemplate)
}

/*onMounted(() => {
  if (entries.value.length === 0) {
    addEmptyEntry()
  }
})*/

const removeEntry = (entry) => {
  const idx = entries.value.indexOf(entry)
  entries.value.splice(idx, 1)
}

const entryUpdated = (entry, updatedEntry) => {
  const idx = entries.value.indexOf(entry)
  entries.value[idx] = updatedEntry
}

const formattedEntries = ref<string>('')

watch(entries, (newEntries) => {
  // map all keys of the entry key and title is same. Add tab intentation after each field after Host line
// Host always needs to be the first line. Order the keys so Host is always at top
  formattedEntries.value = newEntries.map((entry) => {
    const keys = Object.keys(entry).sort((a, b) => {

      if (a === 'Host') {
        return -1
      }

      if (b === 'Host') {
        return 1
      }

      return 0
    })

    return keys.map((key) => {
      let intentation = key === 'Host' ? '' : '\t'
      if (key === 'Host') {
        return `Host ${entry[key] ?? ''}`
      }

      return `${intentation}${key} ${entry[key] ?? ''}`
    }).join('\n')
  }).join('\n\n')
}, {deep: true})

const copyToClipboard = () => {
  navigator.clipboard.writeText(formattedEntries.value)
  toast.success('Copied to clipboard')
}

const pluralizeEntry = computed(() => {
  return entries.value.length > 1 ? 'Entries' : 'Entry'
})


</script>

<template>
  <NuxtLayout title="SSH Config Generator">
    <div class="grid md:grid-cols-2 grid-cols-1 gap-4 my-4">
      <div>
        <div class="flex items-center justify-between">
          <h2 class="md:text-2xl text-lg font-medium tracking-wide">
            {{ entries.length > 0 ? entries.length + ` ${pluralizeEntry}` : 'No Entries' }}</h2>
          <Button @click="addEmptyEntry" type="button">Add New Entry</Button>
        </div>
        <div class="mt-4" v-auto-animate>
          <EntryForm v-if="entries.length > 0" v-for="(entry, idx) in entries" :key="idx" :entry="entry"
                     @remove="removeEntry(entry)"
                     @entriesUpdated="entryUpdated(entry, $event)"/>
          <Card v-else class="border-dashed p-3 h-[30dvh]">
            <CardContent class="grid place-items-center h-full cursor-pointer" @click="addEmptyEntry">
              <div class="space-y-2 flex-col items-center flex">
                <Icon icon="radix-icons:question-mark-circled" class="size-12 text-foreground"/>
                <p class="text-center text-lg text-foreground">No Entries</p>
              </div>
            </CardContent>
          </Card>
        </div>
      </div>
      <Separator class="md:hidden block"/>
      <div>
        <div class="flex items-center justify-between">
          <h2 class="md:text-2xl text-lg font-medium tracking-wide">SSH Config</h2>
          <Button type="button" :disabled="entries.length === 0" @click="copyToClipboard">Copy to Clipboard</Button>
        </div>
        <div class="mt-4" v-auto-animate>
          <Card class="p-3 " v-if="entries.length > 0">
            <pre>{{ formattedEntries }}</pre>
          </Card>
          <Card v-else class="border-dashed p-3 h-[30dvh] cursor-pointer" @click="addEmptyEntry">
            <CardContent class="grid place-items-center h-full">
              <div class="space-y-2 flex-col items-center flex">
                <Icon icon="radix-icons:question-mark-circled" class="size-12 text-foreground"/>
                <p class="text-center text-lg text-foreground">No Entries</p>
              </div>
            </CardContent>
          </Card>
        </div>
      </div>
    </div>
  </NuxtLayout>
</template>

<style scoped>

</style>