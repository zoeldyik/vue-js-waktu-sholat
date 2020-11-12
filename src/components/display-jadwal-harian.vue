<template>
  <div class="display-jadwal">
    <b-col md="8" offset-md="2" class="pt-5">
      <!-- loader -->
      <loader-component v-show="!show"></loader-component>

      <!-- display -->
      <div class="box-display" v-show="show">
        <h3 class="text-center">{{ data.kota.toUpperCase() }}</h3>

        <b-nav tabs align="center" class="mb-3">
          <b-nav-item
            v-for="nav in navs"
            :key="nav"
            :active="selectedNav === nav"
            @click="selectedNav = nav"
          >
            {{ nav }}
          </b-nav-item>
        </b-nav>

        <!-- jadwal hari ini -->
        <b-row no-gutters v-show="selectedNav === 'Hari Ini'">
          <b-col
            cols="6"
            sm="4"
            v-for="jadwal in jadwalSholat"
            :key="jadwal.nama"
          >
            <b-card class="text-center bg-dark text-white m-1">
              <h4>{{ jadwal.nama.toUpperCase() }}</h4>
              <h5>{{ jadwal.value }}</h5>
            </b-card>
          </b-col>
        </b-row>

        <!-- jadwal Bulan Ini -->
        <div class="bulan-Ini" v-show="selectedNav === 'Bulan Ini'">
          <b-card no-body>
            <b-table
              small
              responsive
              striped
              hover
              :items="items"
              :per-page="perPage"
              :current-page="currentPage"
            ></b-table>
          </b-card>
          <b-pagination
            hide-goto-end-buttons
            v-model="currentPage"
            :total-rows="totalRows"
            :per-page="perPage"
            align="left"
            size="sm"
            class="my-1"
          ></b-pagination>
        </div>
      </div>
    </b-col>
  </div>
</template>

<script>
import Loader from "./loader";

export default {
  props: {
    data: {
      type: Object,
      required: true,
    },
  },
  components: {
    "loader-component": Loader,
  },
  data() {
    return {
      url:
        "https://raw.githubusercontent.com/lakuapik/jadwalsholatorg/master/adzan",
      jadwalSholat: [],
      tanggal: {},
      show: false,
      navs: ["Hari Ini", "Bulan Ini"],
      selectedNav: "Hari Ini",
      items: [],
      currentPage: 1,
      perPage: 8,
    };
  },
  computed: {
    totalRows() {
      return this.items.length;
    },
  },
  methods: {
    async getJadwal(data) {
      // console.log("function getJadwal berjalan");

      this.show = false;
      let { kota, tanggal } = data;
      const bulan = tanggal.split("/")[1];
      const tahun = tanggal.split("/")[0];
      tanggal = tanggal.replace(/-/g, "/");

      await this.jadwalHariIni(kota, tanggal);
      await this.jadwalBulanIni(kota, tahun, bulan);
      setTimeout(() => {
        this.show = true;
      }, 1500);
    },
    jadwalHariIni(kota, tanggal) {
      fetch(`${this.url}/${kota}/${tanggal}.json`)
        .then((res) => res.json())
        .then((data) => {
          //   console.log(data);

          const waktu = [
            "imsak",
            "subuh",
            "terbit",
            "dhuha",
            "dzuhur",
            "asar",
            "maghrib",
            "isya",
            "tanggal",
          ];

          // dapatkan values dari data dan urutkan
          const sortedWaktu = Object.values(data).sort();

          // lalu gabungkan dengan array waktu dan push ke properti jadwal
          let TempJadwalSholat = [];
          waktu.forEach((e, i) => {
            TempJadwalSholat.push({
              nama: waktu[i],
              value: sortedWaktu[i],
            });
          });

          this.jadwalSholat = TempJadwalSholat;

          // ambil tanggal dari tempJadwal
          this.tanggal = this.jadwalSholat.pop();
        });
    },
    jadwalBulanIni(kota, tahun, bulan) {
      fetch(`${this.url}/${kota}/${tahun}/${bulan}.json`)
        .then((res) => res.json())
        .then((data) => {
          // console.log(data); this.getJadwal(this.data);

          // ambil tanggal dari data dann rubah tampilan tanggal
          const tempTanggal = data.map((d) => {
            const temp = d.tanggal
              .split("-")
              .reverse()
              .join("-");

            return temp;
          });

          // ganti tanggal dari data dengan tanggal yang sudah di rubah
          data.forEach((e, index) => {
            e.tanggal = tempTanggal[index];
          });

          // console.log(tempTanggal);
          // console.log(data);

          this.items = data;
        });
    },
  },
  mounted() {
    this.getJadwal(this.data);
  },
  watch: {
    data() {
      // console.log(newVal);
      this.getJadwal(this.data);
    },
  },
};
</script>

<style scoped>
a {
  color: #343a40 !important;
}

.nav-tabs {
  border-bottom: unset;
}

.nav-tabs .nav-link.active {
  background-color: unset;
  border: unset;
  border-bottom: 2px solid #343a40;
}

h3 {
  font-weight: 800;
}

h4 {
  opacity: 0.9;
  font-size: 21px;
  font-weight: 800;
}

h5 {
  opacity: 0.8;
}

.pagination >>> .page-link {
  color: #343a40 !important;
}

.pagination >>> .page-link:focus {
  box-shadow: none;
}

.pagination >>> .page-item.active .page-link {
  color: white !important;
  background-color: #343a40 !important;
  border-color: #343a40;
}

@media (max-width: 576px) {
  h4 {
    opacity: 0.9;
    font-size: 16px;
    font-weight: 800;
  }
}
</style>
