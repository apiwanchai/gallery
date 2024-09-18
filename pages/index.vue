<script setup>
import mocData from "../mocdata.json";
const search = ref("");
const selected = ref([]);

const router = useRouter();
const allItems = ref([...mocData]);

const goToDetail = (item) => {
  router.push({
    name: "detail",
    query: { id: item.id },
  });
};

const filteredItems = ref([...allItems.value]);

const formatDate = (isoDateString) => {
  const date = new Date(isoDateString);
  if (isNaN(date.getTime())) return "วันที่ไม่ถูกต้อง";

  const day = String(date.getDate()).padStart(2, "0");
  const month = String(date.getMonth() + 1).padStart(2, "0");
  const year = date.getFullYear();
  return `${day}/${month}/${year}`;
};

const headers = ref([
  { title: "ชื่อผลงาน", value: "nameGallery" },
  { title: "ประเภทผลงาน", value: "typeGallery" },
  { title: "วันที่แสดงผลงาน", value: "startDateShow" },
  { title: "วันที่สิ้นสุดแสดงผลงาน", value: "endDateShow" },
  { title: "สถานะ", value: "status" },
  { title: "Actions", value: "actions", sortable: false },
]);

const searchItems = () => {
  const query = search.value.toLowerCase();
  filteredItems.value = allItems.value.filter((item) => {
    return (
      item.nameGallery.toLowerCase().includes(query) ||
      item.typeGallery.toLowerCase().includes(query)
    );
  });
};

const resetSearch = () => {
  search.value = "";
  filteredItems.value = [...allItems.value];
};

const deleteSelected = () => {
  if (selected.value.length > 0) {
    const updatedItems = allItems.value.filter(
      (item) => !selected.value.includes(item.id)
    );

    allItems.value = updatedItems;

    filteredItems.value = [...updatedItems];

    selected.value = [];
  } else {
    console.log("ไม่มีรายการที่ถูกเลือกสำหรับการลบ");
  }
};

const editDetail = (item) => {
  router.push({
    name: "editDetail",
    query: { id: item.id },
  });
};
</script>
<template>
  <v-container>
    <v-row class="mt-5">
      <v-col cols="6" class="d-flex justify-space-between align-center ga-3">
        <v-text-field
          v-model="search"
          label="ค้นหา"
          class="mt-4"
          variant="outlined"
          elevation="0"
        ></v-text-field>

        <v-icon @click="searchItems">mdi-magnify</v-icon>

        <v-icon @click="resetSearch">mdi-refresh</v-icon>
      </v-col>
      <v-col cols="6" class="d-flex align-center ga-3">
        <v-btn
          class="ml-auto"
          prepend-icon="mdi-delete"
          @click="deleteSelected"
        >
          Delete
        </v-btn>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="12">
        <v-data-table
          :headers="headers"
          :items="filteredItems"
          item-value="id"
          show-select
          v-model="selected"
          class="elevation-1"
          :items-per-page="5"
          :footer-props="{
            'items-per-page-options': { value: 5, title: '5' },
          }"
        >
          <template v-slot:item.status="{ item }">
            <v-chip :color="item.status === 'แสดง' ? 'green' : 'red'"   variant="tonal">
              <v-icon :icon="item.status === 'แสดง' ? 'mdi-checkbox-marked-outline': 'mdi-alpha-x-box-outline'" start></v-icon>
              {{ item.status }}
            </v-chip>
          </template>
          <template v-slot:item.startDateShow="{ item }">
            {{ item.status === "แสดง" ? formatDate(item.startDateShow) : "-" }}
          </template>
          <template v-slot:item.endDateShow="{ item }">
            {{ item.status === "แสดง" ? formatDate(item.endDateShow) : "-" }}
          </template>

          <template v-slot:item.actions="{ item }">
            <v-tooltip text="รายละเอียด" location="bottom">
              <template v-slot:activator="{ props }">
                <v-icon color="grey" v-bind="props" @click="goToDetail(item)"
                  >mdi-eye
                </v-icon>
              </template>
            </v-tooltip>
            
            <v-tooltip text="แก้ไข" location="bottom">
              <template v-slot:activator="{ props }">
                <v-icon  v-bind="props" color="yellow" @click="editDetail(item)">mdi-pencil</v-icon>
              </template>
            </v-tooltip>

           
          </template>
        </v-data-table>
      </v-col>
    </v-row>
  </v-container>
</template>



<style scoped></style>
