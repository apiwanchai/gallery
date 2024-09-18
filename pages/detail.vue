<script setup>
import { useRoute, useRouter } from "vue-router";
import mocData from "../mocdata.json";

const route = useRoute();
const router = useRouter();
const id = ref(route.query.id);
const link = reactive([
  { title: "หน้าหลัก", disabled: false, href: "/" },
  { title: "การแสดงผลงาน", disabled: true, href: "" },
]);

const description =
  "Lorem Ipsum is simply dummy text of the printing and typesetting industry.";
const gallery = computed(() =>
  mocData.find((item) => item.id === parseInt(id.value))
);
const expanded = ref(false);

const formatDate = (isoDateString) => {
  const date = new Date(isoDateString);
  return isNaN(date.getTime())
    ? "วันที่ไม่ถูกต้อง"
    : date.toLocaleDateString("th-TH");
};

const editDetail = () =>
  router.push({ name: "editDetail", query: { id: id.value } });

const lineClamp = ref(3);

const toggleExpand = () => {
  expanded.value = !expanded.value;
  lineClamp.value = expanded.value ? 5 : 3;
};
</script>

<template>
  <v-container>
    <v-breadcrumbs :items="link" class="mb-4">
      <template v-slot:title="{ item }">
        {{ item.title.toUpperCase() }}
      </template>
    </v-breadcrumbs>

    <v-row>
      <v-col cols="12" md="6">
        <v-carousel cycle hide-delimiters height="300">
          <v-carousel-item v-for="(item, i) in gallery.image" :key="i">
            <v-img :src="item.src" :alt="item.alt"></v-img>
          </v-carousel-item>
        </v-carousel>

        <v-card-text class="mt-4">
          <p>
            Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
            eiusmod tempor incididunt ut labore et dolore magna aliqua.
          </p>
          <p>
            Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris
            nisi ut aliquip ex ea commodo consequat.
          </p>
        </v-card-text>
      </v-col>

      <v-col cols="12" md="6">
        <v-card class="pa-4" height="700">
          <div class="d-flex justify-end mb-2">
            <v-btn
              icon="mdi-arrow-left"
              size="small"
              variant="outlined"
              color="black"
              elevation="0"
              class="mr-2"
              @click="router.push('/')"
            ></v-btn>
            <v-btn
              icon="mdi-image-edit-outline"
              size="small"
              variant="outlined"
              color="primary"
              elevation="0"
              @click="editDetail"
            ></v-btn>
          </div>

          <v-card-title>{{ gallery.nameGallery }}</v-card-title>
          <v-card-subtitle>{{ description }}</v-card-subtitle>

          <v-row class="mt-4 px-4">
            <v-col cols="6">
              <p class="text-grey">
                วันที่แสดง:
                <span class="text-black">{{
                  gallery.status === "แสดง"
                    ? formatDate(gallery.startDateShow)
                    : "สิ้นสุดวันแสดง"
                }}</span>
              </p>
              <p class="text-grey">
                ลักษณะผลงาน:
                <span class="text-black">{{ gallery.typeGallery }}</span>
              </p>
            </v-col>
            <v-col cols="6">
              <p class="text-grey">
                ประเภทผลงาน:
                <span class="text-black">{{ gallery.typeGallery }}</span>
              </p>
            </v-col>
          </v-row>

          <v-divider class="my-4"></v-divider>

          <v-card-title>รายชื่อเจ้าของผลงาน</v-card-title>
          <v-expansion-panels>
            <v-expansion-panel v-for="i in 3" :key="i">
              <v-expansion-panel-title>{{
                gallery.nameArtis
              }}</v-expansion-panel-title>
              <v-expansion-panel-text>
                <p class="text-grey">
                  เบอร์โทร: <span class="text-black">081-234-5678</span>
                </p>
                <p class="text-grey">
                  อีเมล: <span class="text-black">mail@gmail.com</span>
                </p>
              </v-expansion-panel-text>
            </v-expansion-panel>
          </v-expansion-panels>

          <v-divider class="my-4"></v-divider>

          <v-card-text>
            <div
              :style="{
                display: '-webkit-box',
                WebkitBoxOrient: 'vertical',
                WebkitLineClamp: lineClamp,
                overflow: 'hidden',
              }"
            >
              Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do
              eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut
              enim ad minim veniam, quis nostrud exercitation ullamco laboris
              nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in
              reprehenderit in voluptate velit esse cillum dolore eu fugiat
              nulla pariatur. Excepteur sint occaecat cupidatat non proident,
              sunt in culpa qui officia deserunt mollit anim id est laborum.
            </div>
            <v-btn text color="blue" @click="toggleExpand">
              {{ expanded ? "ย่อ" : "อ่านเพิ่มเติม" }}
            </v-btn>
          </v-card-text>
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
