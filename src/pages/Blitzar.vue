<template>
  <q-page class="flex flex-center">
    <div class="q-pa-md">
      <BlitzForm :schema="schema" v-model="formData" :columnCount="1" />
    </div>
    <div class="q-pa-md" style="max-width: 350px">
      <q-list bordered>
        <q-item v-for="item in items" :key="item.id" clickable v-ripple>
          <q-item-section>{{ item.title }}</q-item-section>
          <q-item-section avatar>
            <q-icon v-if="item.isDone" color="positive" name="done" />
            <q-icon v-else color="negative" name="close" />
          </q-item-section>
        </q-item>
      </q-list>
    </div>
  </q-page>
</template>

<script>
import { BlitzForm } from "blitzar";
import Vue from "vue";
import { QInput, QToggle } from "quasar";
Vue.component("QInput", QInput);
Vue.component("QToggle", QToggle);
const schema = [
  {
    id: "title",
    component: "QInput",
    label: "Title",
    subLabel: "An irresistable sublabel"
  },
  {
    id: "isDone",
    label: "Done",
    subLabel: "Pound that Done button baby!",
    component: "QToggle"
  }
];
export default {
  name: "PageIndex",
  components: { BlitzForm },
  data() {
    return {
      schema,
      formData: {},
      items: [
        { title: "Now you see me", isDone: true },
        { title: "Now you don't", isDone: false }
      ]
    };
  }
};
</script>
