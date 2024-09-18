<script setup>
import { useRoute, useRouter } from "vue-router";
import mocData from "../mocdata.json";

const route = useRoute();
const router = useRouter();
const id = ref(route.query.id);

const link = reactive([
  {
    title: "หน้าหลัก",
    disabled: false,
    href: "/",
  },
  {
    title: "แก้ไขการแสดงผลงาน",
    disabled: true,
    href: "",
  },
]);

const categories = ref(["ศิลปกรรม", "วรรณกรรม", "ภาพยนตร์"]);

const selectedCategory = ref(null);
const workTitle = ref("");

const ownerFirstName = ref("");
const ownerLastName = ref("");

const status = ref(null);
const startDate = ref(null);
const endDate = ref(null);
const details = ref("");

const formatDateToISOString = (date) => {
  return date ? new Date(date).toISOString() : null;
};
const setToMidnight = (dateString) => {
  const date = new Date(dateString);

  if (!isNaN(date)) {
    date.setHours(0, 0, 0, 0);
    return date;
  }
  return null;
};

const loadData = () => {
  const item = mocData.find((item) => item.id == id.value);

  if (item) {
    workTitle.value = item.nameGallery;
    selectedCategory.value = item.typeGallery;
    startDate.value = setToMidnight(item.startDateShow);
    endDate.value = setToMidnight(item.endDateShow);
    status.value = item.status;
    details.value = item.detail;
    const nameParts = item.nameArtis.split(" ");
    ownerLastName.value = nameParts[1];
    ownerFirstName.value = nameParts[0];
  } else {
    console.log("Item not found");
  }
};

const editDetail = () => {
  const itemIndex = mocData.findIndex((item) => item.id == id.value);

  if (itemIndex !== -1) {
    mocData[itemIndex].nameGallery = workTitle.value;
    mocData[itemIndex].typeGallery = selectedCategory.value;
    mocData[itemIndex].startDateShow = formatDateToISOString(startDate.value);
    mocData[itemIndex].endDateShow = formatDateToISOString(endDate.value);
    mocData[itemIndex].status = status.value;
    mocData[itemIndex].detail = details.value;
    mocData[
      itemIndex
    ].nameArtis = `${ownerFirstName.value} ${ownerLastName.value}`;
    navigateTo("/");
    console.log("Updated mocData:", mocData[itemIndex]);
  } else {
    console.log("Item not found");
  }
};

const goBack = () => {
  navigateTo("/");
};

onMounted(() => {
  loadData();
});
</script>

<template>
  <v-breadcrumbs :items="link">
    <template v-slot:title="{ item }">
      {{ item.title.toUpperCase() }}
    </template>
  </v-breadcrumbs>
  <v-container class="pa-8">
    <v-row>
      <v-col cols="12">
        <h2>แก้ไขการแสดงผลงาน</h2>
      </v-col>
    </v-row>

    <v-form>
      <v-row>
        <v-col cols="12" sm="6" md="6">
          <v-select
            v-model="selectedCategory"
            :items="categories"
            label="ประเภทผลงาน"
            variant="outlined"
            required
          ></v-select>
        </v-col>

        <v-col cols="12" sm="6" md="6">
          <v-text-field
            v-model="workTitle"
            label="ชื่อผลงาน"
            variant="outlined"
            required
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row>
        <v-col cols="12" sm="6">
          <v-text-field
            v-model="ownerFirstName"
            label="ชื่อ"
            variant="outlined"
            required
          ></v-text-field>
        </v-col>

        <v-col cols="12" sm="6">
          <v-text-field
            v-model="ownerLastName"
            label="นามสกุล"
            variant="outlined"
            required
          ></v-text-field>
        </v-col>
      </v-row>

      <v-card class="mt-4">
        <v-card-title>ข้อมูลผลงาน</v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12" sm="6">
              <v-radio-group v-model="status" label="ระบบแสดงการแสดงผลงาน">
                <v-radio label="แสดง" value="แสดง"></v-radio>
                <v-radio label="ไม่แสดง" value="ไม่แสดง"></v-radio>
              </v-radio-group>
            </v-col>

            <v-col cols="3" sm="3">
              <v-date-input
                label="วันที่เริ่ม"
                v-model="startDate"
                :disabled="status === 'ไม่แสดง' || status === null"
                variant="solo"
                :max="endDate || undefined"
              ></v-date-input>
            </v-col>

            <v-col cols="3" sm="3">
              <v-date-input
                label="วันที่สิ้นสุด"
                v-model="endDate"
                variant="solo"
                :disabled="status === 'ไม่แสดง' || status === null"
                :min="startDate || undefined"
              ></v-date-input>
            </v-col>
          </v-row>

          <v-row>
            <v-col cols="12">
              <v-textarea v-model="details" label="รายละเอียด"></v-textarea>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>

      <v-row class="mt-4">
        <v-col cols="12">
          <v-btn class="mr-2" color="black" @click="editDetail"> บันทึก </v-btn>
          <v-btn text @click="goBack"> กลับ </v-btn>
        </v-col>
      </v-row>
    </v-form>
  </v-container>
</template>
