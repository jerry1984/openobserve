<!-- Copyright 2023 Zinc Labs Inc.

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU Affero General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU Affero General Public License for more details.

You should have received a copy of the GNU Affero General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
-->
<template>
  <div class="bg-white">
    <div class="flex justify-between items-center q-px-md q-py-sm">
      <div style="font-size: 18px">
        {{ t("iam.addRole") }}
      </div>
      <q-icon name="cancel" class="cursor-pointer" size="20px"></q-icon>
    </div>

    <div class="full-width bg-grey-4" style="height: 1px" />

    <div class="q-px-md q-mt-md o2-input">
      <q-input
        v-model.trim="name"
        :label="t('common.name') + ' *'"
        color="input-border"
        bg-color="input-bg"
        class="q-py-md showLabelOnTop"
        stack-label
        outlined
        filled
        dense
        :rules="[(val, rules) => !!val || t('common.nameRequired')]"
      />

      <div class="flex justify-center q-mt-lg">
        <q-btn
          data-test="add-alert-cancel-btn"
          v-close-popup="true"
          class="q-mb-md text-bold"
          :label="t('alerts.cancel')"
          text-color="light-text"
          padding="sm md"
          no-caps
          @click="$emit('cancel:hideform')"
        />
        <q-btn
          data-test="add-alert-submit-btn"
          :label="t('alerts.save')"
          class="q-mb-md text-bold no-border q-ml-md"
          color="secondary"
          padding="sm xl"
          no-caps
          @click="saveRole"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { createRole, updateRole } from "@/services/iam";
import { useQuasar } from "quasar";
import { ref, computed } from "vue";
import { useI18n } from "vue-i18n";
import { useStore } from "vuex";

const { t } = useI18n();
const props = defineProps({
  width: {
    type: String,
    default: "30vw",
  },
  role: {
    type: Object,
    default: () => null,
  },
  org_identifier: {
    type: String,
    default: "",
  },
});

const emits = defineEmits(["cancel:hideform", "added:role"]);

const name = ref(props.role?.name || "");

const store = useStore();

const q = useQuasar();

const saveRole = () => {
  createRole(name.value, store.state.selectedOrganization.identifier)
    .then(() => {
      emits("cancel:hideform");
      emits("added:role");
      q.notify({
        message: `Role "${name.value}" Created Successfully!`,
        color: "positive",
        position: "bottom",
        timeout: 3000,
      });
    })
    .catch((err) => {
      q.notify({
        message: "Error while creating role",
        color: "negative",
        position: "bottom",
        timeout: 3000,
      });
      console.log(err);
    });
};
</script>

<style scoped></style>
