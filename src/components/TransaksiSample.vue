<template>
  <div>
    <main>
      <div class="d-flex justify-content-between align-items-center">
        <h1 class="h3 mb-0 text-gray-800">Transaksi</h1>
        <router-link
          v-if="role === 'admin' || 'kasir'"
          to="/tambah-transaksi"
          class="btn bg-gradient-primary btn-icon-split text-light mr-2"
        >
          <span class="icon text-white-50">
            <i class="fas fa-plus"></i>
          </span>
          <span class="text">Tambah</span>
        </router-link>
      </div>
      <div class="card mt-4 mb-4">
        <div class="card-body">
          <form>
            <div class="row">
              <div class="col-lg-6 col-md-6">
                <div class="input-group mb-3">
                  <div class="input-group-prepend">
                    <span class="input-group-text" id="basic-addon1">
                      <i class="fas fa-sun"></i>
                    </span>
                  </div>
                  <b-form-select
                    class="form-control"
                    @change="getData($event)"
                    v-model="tahun"
                    :options="list_years"
                  ></b-form-select>
                </div>
              </div>
              <div class="col-lg-6 col-md-6">
                <div class="input-group mb-3">
                  <div class="input-group-prepend">
                    <span class="input-group-text" id="basic-addon1">
                      <i class="fas fa-moon"></i>
                    </span>
                  </div>
                  <b-form-select
                    class="form-control"
                    @change="getData($event)"
                    v-model="bulan"
                    :options="list_months"
                  ></b-form-select>
                </div>
              </div>
            </div>
          </form>
          <table class="table table-bordered" id="dataTable">
            <tr>
              <th>#</th>
              <th>Nama Member</th>
              <th>Tanggal</th>
              <th>Status Cucian</th>
              <th>Status Pembayaran</th>
              <th>Tanggal Bayar</th>
              <th>Kasir</th>
              <th>Total</th>
              <th>Aksi</th>
            </tr>
            <tr v-for="(saksi, index) in transaksi" :key="index">
              <td>{{ index + 1 }}</td>
              <td>{{ saksi.nama_member }}</td>
              <td>{{ saksi.tanggal }}</td>
              <td>
                <select
                  class="form-control"
                  id="form_control_status_pembayaran"
                  :disabled="role === 'owner'"
                  @change="changeStatus(saksi.id_transaksi, $event)"
                >
                  <option
                    value="baru"
                    v-if="saksi.status_cucian === 'baru'"
                    :selected="saksi.status_cucian === 'baru'"
                  >
                    Baru
                  </option>
                  <option
                    value="proses"
                    :selected="saksi.status_cucian === 'proses'"
                  >
                    Proses
                  </option>
                  <option
                    value="selesai"
                    :selected="saksi.status_cucian === 'selesai'"
                  >
                    Selesai
                  </option>
                  <option
                    value="diambil"
                    :selected="saksi.status_cucian === 'diambil'"
                  >
                    Diambil
                  </option>
                </select>
              </td>
              <td>
                <select
                  :disabled="role === 'owner'"
                  class="form-control"
                  @change="changeBayar(saksi.id_transaksi, $event)"
                >
                  <option
                    value="dibayar"
                    :selected="saksi.status_pembayaran === 'dibayar'"
                  >
                    Dibayar
                  </option>
                  <option
                    value="belum_dibayar"
                    :selected="saksi.status_pembayaran === 'belum_dibayar'"
                  >
                    Belum Dibayar
                  </option>
                </select>
              </td>
              <td>{{ saksi.tanggal_bayar }}</td>
              <td>{{ saksi.kasir }}</td>
              <td>{{ saksi.total }}</td>
              <td>
                <a
                  v-b-modal.modal_detail
                  href="#"
                  class="btn btn-warning"
                  @click="detail(saksi.detail_transaksi, saksi.total)"
                >
                  <i class="fas fa-eye"></i>
                </a>
              </td>
            </tr>
          </table>
        </div>
      </div>
    </main>

    <b-modal
      id="modal_detail"
      ref="modal"
      title="Detail Transaksi"
      size="lg"
      hide-footer="true"
    >
      <a
        v-if="role === 'kasir'"
        href="#"
        @click="cetak()"
        class="btn bg-gradient-primary btn-icon-split text-light mr-2 mb-3"
      >
        <span class="icon text-white-50">
          <i class="fas fa-print"></i>
        </span>
        <span class="text">Print Struk</span>
      </a>
      <!-- <b-table :items="detail_transaksi"> </b-table> -->
      <table class="table table-bordered" id="printed">
        <tr>
          <th>#</th>
          <th>Jenis Paket</th>
          <th>Berat</th>
          <th>Sub Total</th>
        </tr>
        <tr v-for="(det, index) in detail_transaksi" :key="index">
          <td>{{ index + 1 }}</td>
          <td>{{ det.jenis_paket }}</td>
          <td>{{ det.berat }}</td>
          <td>{{ det.sub_total }}</td>
        </tr>
        <tr>
          <td colspan="3" class="h3 text-gray-900">Total</td>
          <td class="h3 text-danger font-weight-bold text-danger">
            Rp {{ total }}
          </td>
        </tr>
      </table>
    </b-modal>
  </div>
</template>

<script>
module.exports = {
  data: function () {
    return {
      role: "",
      id_transaksi: "",
      nama_member: "",
      tanggal: "",
      status_cucian: "",
      status_pembayaran: "",
      tanggal_bayar: "",
      kasir: "",
      total: "",
      tahun: "",
      bulan: "",
      tanggal: "",
      action: "",
      total: "",
      transaksi: [],
      detail_transaksi: [],
      list_years: [2020, 2021, 2022, 2023],
      list_months: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12],
    };
  },

  methods: {
    getData: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      let form = {
        tahun: this.tahun,
        bulan: this.bulan,
      };

      axios
        .post(base_url + "/transaksi/report", form, config)
        .then((response) => {
          this.transaksi = response.data.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },

    getInfo: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      axios.get(base_url + "/user/login/check", config).then((response) => {
        if (response.data.success == true) {
          this.role = response.data.data.role;

          console.log(response.data.data.role);
        }
      });
    },

    changeStatus: function (id_transaksi, event) {
      let conf = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      let form = {
        id_transaksi: id_transaksi,
        status_cucian: event.target.value,
      };

      axios
        .put(base_url + "/transaksi/status", form, conf)
        .then((response) => {
          this.getData();
          alert(response.data.message);
        })
        .catch((error) => {
          console.log(error);
        });
    },

    changeBayar: function (id_transaksi, event) {
      let conf = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      let form = {
        id_transaksi: id_transaksi,
        status_pembayaran: event.target.value,
      };

      axios
        .put(base_url + "/transaksi/bayar", form, conf)
        .then((response) => {
          this.getData();

          alert(response.data.message);
        })
        .catch((error) => {
          console.log(error);
        });
    },

    detail: function (detail_transaksi, total) {
      this.total = total;
      this.detail_transaksi = detail_transaksi;
    },

    cetak: function () {
      const prtHtml = document.getElementById("printed").innerHTML;

      let stylesHtml = "";
      for (const node of [
        ...document.querySelectorAll('link[rel="stylesheet"], style'),
      ]) {
        stylesHtml += node.outerHTML;
      }

      const WinPrint = window.open(
        "",
        "",
        "left=300,top=0,width=800,height=900,toolbar=0,scrollbars=0,status=0"
      );

      WinPrint.document.write(`
      <!DOCTYPE html>
      <html>
        <head>
          <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">

        </head>
        <body>
          ${prtHtml}
        </body>
      </html>
      `);
      WinPrint.document.close();
      WinPrint.focus();
      WinPrint.print();
      //WinPrint.close();
    },
  },

  mounted() {
    this.getInfo();
    this.getData();
  },
};
</script>
