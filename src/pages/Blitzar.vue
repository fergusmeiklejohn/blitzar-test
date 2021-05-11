<template>
  <q-page class="flex flex-center">
    <div class="column items-center">
      <div class="q-pa-md row">
        <BlitzForm
          :key="formKey"
          :schema="schema"
          v-model="formData"
          :columnCount="1"
          :actionButtons="['edit', 'cancel', 'save']"
          @save="onSave"
        />
      </div>
      <div class="q-pa-md row" style="max-width: 350px">
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
      <div class="q-pa-md row">
        <BlitzTable
          @row-click="editRow"
          :schemaColumns="schemaColumns"
          :schemaGrid="schemaColumns"
          :rows="items"
          title="Todos"
          flat
          bordered
        />
      </div>
    </div>
  </q-page>
</template>

<script>
import { BlitzForm, BlitzTable } from "blitzar";
import Vue from "vue";
import { QInput, QToggle, QCheckbox } from "quasar";
Vue.component("QInput", QInput);
Vue.component("QToggle", QToggle);
Vue.component("QCheckbox", QCheckbox);
const schema = [
  {
    id: "title",
    component: "QInput",
    label: "Title"
  },
  {
    id: "isDone",
    label: "Done",
    component: "QCheckbox"
  }
];
const schemaColumns = [
  { id: "title", lable: "Title", component: "QInput" },
  {
    id: "done",
    lable: "Done",
    component: "QInput",
    parseValue: (val, { formData, fieldInput }) => {
      const value = formData.isDone ? "✅" : "❌";
      fieldInput({ id: "done", value });
      return value;
    }
  }
];
export default {
  name: "PageIndex",
  components: { BlitzForm, BlitzTable },
  data() {
    return {
      schema,
      formKey: 0,
      formData: {},
      items: [
        { title: "Now you see me", isDone: true },
        { title: "Now you don't", isDone: false }
      ],
      schemaColumns: schemaColumns
    };
  },
  methods: {
    onSave({ newData }) {
      this.formData = { title: "", isDone: false };
      this.formKey++;
      this.items.push(newData);
    },
    editRow(event, rowData) {
      let title = rowData.title;
      let isDone = rowData.isDone;
      this.formData = { title, isDone };
      this.formKey++;
    }
  }
};
</script>
