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
          @cancel="onCancel"
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
import { QInput, QToggle, QCheckbox, Dialog } from "quasar";
Vue.component("QInput", QInput);
Vue.component("QToggle", QToggle);
Vue.component("QCheckbox", QCheckbox);

// Firestore users have 28 char ids
// Firestore docs have 20 char ids
function createRandomID(len) {
  const CHARS =
    "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
  const Alpha = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz";

  let id = "";

  for (let i = 0; i < len; i++) {
    if (i === 0) {
      id += Alpha.charAt(Math.floor(Math.random() * CHARS.length));
    } else {
      id += CHARS.charAt(Math.floor(Math.random() * CHARS.length));
    }
  }
  return id;
}

const schema = [
  {
    id: "title",
    component: "QInput",
    label: "Title",
    defaultValue: ""
  },
  {
    id: "isDone",
    label: "Done",
    component: "QCheckbox",
    defaultValue: false
  }
];
const schemaColumns = [
  {
    id: "edit-btn",
    component: "button",
    slot: "Edit",
    mode: "view",
    events: {
      click: (event, { formData }) => {
        console.log("formData: ", formData);
      }
    }
  },
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
        { docId: "5HG4KA468UbcuzezQP3", title: "Now you see me", isDone: true },
        { docId: "AP08fuLBpr3TRuxd3Yrx", title: "Now you don't", isDone: false }
      ],
      schemaColumns: schemaColumns
    };
  },
  methods: {
    onSave({ newData, oldData }) {
      this.formData = { title: "", isDone: false };
      let id = oldData.docId ?? createRandomID(20);
      if (oldData.docId) {
        this.items = this.items.filter(item => item.docId !== oldData.docId);
      }
      let item = { docId: id, ...newData };
      this.items.push(item);
      this.formKey++;
    },
    onCancel() {
      this.formData = { title: "", isDone: false };
      this.formKey++;
    },
    editRow(event, rowData) {
      console.log(rowData);
      let docId = rowData.docId;
      let title = rowData.title;
      let isDone = rowData.isDone;
      this.formData = { docId, title, isDone };
      this.formKey++;
    }
  }
};
</script>
