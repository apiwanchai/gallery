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
    title: "การแสดงผลงาน",
    disabled: true,
    href: "",
  },
]);

const description =
  "Lorem Ipsum is simply dummy text of the printing and typesetting industry.";

const gallery = computed(() => {
  return mocData.find((item) => item.id === parseInt(id.value));
});

const formatDate = (isoDateString) => {
  const date = new Date(isoDateString);
  if (isNaN(date.getTime())) return "วันที่ไม่ถูกต้อง";

  const day = String(date.getDate()).padStart(2, "0");
  const month = String(date.getMonth() + 1).padStart(2, "0");
  const year = date.getFullYear();
  return `${day}/${month}/${year}`;
};

const expanded = ref(false);
const shortenedDetail = computed(() =>
  expanded.value
    ? gallery.value.detail
    : gallery.value.detail.slice(0, 100) + "..."
);

const editDetail = () => {
  router.push({
    name: "editDetail",
    query: { id: id.value },
  });
};
</script>

<template>
  <v-breadcrumbs :items="link">
    <template v-slot:title="{ item }">
      {{ item.title.toUpperCase() }}
    </template>
  </v-breadcrumbs>
  <v-container>
    <v-row>
      <v-col cols="12" md="6">
        <v-carousel cycle hide-delimiters height="300">
          <v-carousel-item v-for="(item, i) in gallery.image" :key="i">
            <v-img :src="item.src" :alt="item.alt"></v-img>
          </v-carousel-item>
        </v-carousel>
        <p class="mt-5">
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
          eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad
          minim veniam, quis nostrud exercitation ullamco laboris nisi ut
          aliquip ex ea commodo consequat.
        </p>
        <p class="mt-5">
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
          eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad
          minim veniam, quis nostrud exercitation ullamco laboris nisi ut
          aliquip ex ea commodo consequat.
        </p>
        <p class="mt-5">
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
          eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad
          minim veniam, quis nostrud exercitation ullamco laboris nisi ut
          aliquip ex ea commodo consequat.
        </p>
        <p class="mt-5">
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
          eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad
          minim veniam, quis nostrud exercitation ullamco laboris nisi ut
          aliquip ex ea commodo consequat.
        </p>
      </v-col>

      <v-col cols="12" md="6">
        <v-card class="pa-4" height="700">
          <div class="d-flex justify-end">
            <div>
              <v-btn
                icon="mdi-arrow-left"
                size="small"
                variant="outlined"
                color="black"
                elevation="0"
                class="mr-2"
                @click="navigateTo('/')"
              ></v-btn>
              <v-btn
                icon="mdi-image-edit-outline"
                size="small"
                variant="outlined"
                color="primary"
                elevation="0"
                @click="editDetail()"
              ></v-btn>
            </div>
          </div>

          <v-card-title>{{ gallery.nameGallery }}</v-card-title>
          <v-card-subtitle>{{ description }}</v-card-subtitle>
          <v-row class="pa-4">
            <v-col cols="6">
              <p class="text-grey">
                วันที่แสดง :
                <span class="text-black">
                  {{
                    gallery.status === "แสดง"
                      ? formatDate(gallery.startDateShow)
                      : "สิ้นสุดวันแสดง"
                  }}</span
                >
              </p>
              <p class="text-grey">
                ลักษณะผลงาน :
                <span class="text-black">{{ gallery.typeGallery }}</span>
              </p>
            </v-col>
            <v-col cols="6">
              <p class="text-grey">
                ประเภทผลงาน :
                <span class="text-black"> {{ gallery.typeGallery }} </span>
              </p>
            </v-col>
          </v-row>

          <v-card-title class="">รายชื่อเจ้าของผลงาน</v-card-title>
          <v-expansion-panels
            variant="accordion"
            elevation="1"
            class="px-4 b"
            rounded="xl"
          >
            <v-expansion-panel
              v-for="i in 3"
              :key="i"
              :title="gallery.nameArtis"
            >
              <v-expansion-panel-text>
                <p class="text-grey">
                  เบอรโทร : <span class="text-black">081-234-5678</span>
                </p>
                <p class="text-grey">
                  อีเมล : <span class="text-black">mail@gmail.com</span>
                </p>
              </v-expansion-panel-text>
            </v-expansion-panel>
          </v-expansion-panels>

          <div class="pa-5">
            <p>{{ shortenedDetail }}</p>
            <p class="text-blue" v-if="!expanded" @click="expanded = true">
              อ่านเพิ่มเติม
            </p>
            <p class="text-blue" v-if="expanded" @click="expanded = false">
              ย่อ
            </p>
          </div>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped>
v-container {
  margin-top: 20px;
}
</style>
