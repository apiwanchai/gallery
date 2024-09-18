<script setup>
import mocData from "../mocdata.json";


const search = ref("");
const selected = ref([]);
const page = ref(1); 
const itemsPerPage = ref(5);

const router = useRouter();
const allItems = ref([...mocData]);


const totalItems = allItems.value.length;
const totalPages = computed(() => Math.ceil(totalItems / itemsPerPage.value));

const goToDetail = (item) => {
  router.push({
    name: "detail",
    query: { id: item.id },
  });
};


const paginatedItems = computed(() => {
  const start = (page.value - 1) * itemsPerPage.value;
  const end = start + itemsPerPage.value;
  return allItems.value.slice(start, end);
});

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
  const filtered = allItems.value.filter((item) => {
    return (
      item.nameGallery.toLowerCase().includes(query) ||
      item.typeGallery.toLowerCase().includes(query)
    );
  });
 
  allItems.value = filtered;
  page.value = 1; 
};

const resetSearch = () => {
  search.value = "";
  allItems.value = [...mocData]; 
  page.value = 1; 
};

const deleteSelected = () => {
  if (selected.value.length > 0) {
    const updatedItems = allItems.value.filter(
      (item) => !selected.value.includes(item.id)
    );

    allItems.value = updatedItems;
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
    <h2 class="mt-5">รายการผลงาน</h2>
    <v-row>
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
          :items="paginatedItems"
          item-value="id"
          show-select
          v-model="selected"
          class="elevation-1"
          :items-per-page="itemsPerPage"
          hide-default-footer
        >
          <template v-slot:item.status="{ item }">
            <v-chip
              :color="item.status === 'แสดง' ? 'green' : 'red'"
              variant="tonal"
            >
              <v-icon
                :icon="
                  item.status === 'แสดง'
                    ? 'mdi-checkbox-marked-outline'
                    : 'mdi-alpha-x-box-outline'
                "
                start
              ></v-icon>
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
            <div class="icon-container">
              <div class="tooltip-wrapper">
                <v-icon color="grey" @click="goToDetail(item)">mdi-eye</v-icon>
                <span class="tooltip-text">รายละเอียด</span>
              </div>

              <div class="tooltip-wrapper">
                <v-icon color="yellow" @click="editDetail(item)"
                  >mdi-pencil</v-icon
                >
                <span class="tooltip-text">แก้ไข</span>
              </div>
            </div>
          </template>
        </v-data-table>

    
        <v-row class="d-flex align-center mt-4">
          <v-col cols="2">
            <v-select
              v-model="itemsPerPage"
              :items="[5, 10, 15]"
              label="Page Size"
              hide-details
              variant="outlined"
              style="width: 100px"
            ></v-select>
          </v-col>

        
          <v-pagination
            v-model="page"
            :length="totalPages"
            total-visible="1"
            show-first-last-page
            class="ml-auto"
          ></v-pagination>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped>
.icon-container {
  display: flex;
  gap: 10px; 
}

.tooltip-wrapper {
  position: relative;
  display: inline-block;
}

.tooltip-text {
  visibility: hidden;
  width: 80px;
  background-color: black;
  color: #fff;
  text-align: center;
  padding: 5px;
  border-radius: 5px;


  position: absolute;
  bottom: 125%;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1;
  

  opacity: 0;
  transition: opacity 0.3s;
}

.tooltip-wrapper:hover .tooltip-text {
  visibility: visible;
  opacity: 1;
}
</style>