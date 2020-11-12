<template>
  <div>
    <b-container>
      <b-row>
        <b-col sm="8" offset-sm="2" md="4" offset-md="4">
          <h3 class="text-center mt-4 mb-4">Cari Jadwal Sholat</h3>
          <b-row no-gutters>
            <b-col cols="9" class="pr-1">
              <b-form-select
                id="select"
                v-model="selectedKota"
                :options="options"
              ></b-form-select>
            </b-col>

            <b-col cols="3">
              <b-btn variant="dark" class="btn-block" @click="getJadwal">
                Get
              </b-btn>
            </b-col>
          </b-row>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
export default {
  data() {
    const now = new Date();
    const dateNow = `${now.getFullYear()}/${now.getMonth() + 1}/${
      now.getDate() < 10 ? "0" + now.getDate() : now.getDate()
    }`;

    return {
      apiUrl:
        "https://raw.githubusercontent.com/lakuapik/jadwalsholatorg/master/kota.json",
      options: [],
      selectedKota: null,
      tanggal: dateNow,
    };
  },
  computed: {},
  methods: {
    getKota() {
      fetch(this.apiUrl)
        .then((res) => res.json())
        .then((data) => {
          // console.log(data);
          this.options = data.map((kota) => {
            return {
              value: kota,
              text: kota,
            };
          });

          this.options.unshift({ value: null, text: "pilih kota" });
        });
    },
    getJadwal() {
      if (this.selectedKota) {
        // console.log("emit function from control component");
        const data = { kota: this.selectedKota, tanggal: this.tanggal };
        this.$emit("get-jadwal", data);
      }
    },
  },
  created() {
    this.getKota();
  },
};
</script>

<style>
h3 {
  font-weight: 600;
}

.custom-select {
  font-size: 14px !important;
}

.btn {
  font-size: 12px !important;
}
</style>
