<template>
  <div>
    <main>
      <div class="d-flex justify-content-between align-items-center">
        <h1 class="h3 mb-0 text-gray-800">Outlet</h1>
        <router-link
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
          <div class="card-body">
            <form>
              <div class="row">
                <div class="col-lg-6 col-md-6">
                  <div class="form-group">
                    <label for="tahun" class="col-form-label">Tahun</label>
                    <b-form-select
                      class="form-control"
                      @change="getData($event)"
                      v-model="tahun"
                      :options="list_years"
                    ></b-form-select>
                  </div>
                </div>
                <div class="col-lg-6 col-md-6">
                  <div class="form-group">
                    <label for="tahun" class="col-form-label">Bulan</label>
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
            <table class="table">
              <tr>
                <td>#</td>
                <td>Nama Member</td>
                <td>Tanggal</td>
                <td>Status Cucian</td>
                <td>Status Pembayaran</td>
                <td>Tanggal Bayar</td>
                <td>Kasir</td>
                <td>Total</td>
                <td>Aksi</td>
              </tr>
              <tr v-for="(saksi, index) in transaksi" :key="index">
                <td>{{ index + 1 }}</td>
                <td>{{ saksi.nama_member }}</td>
                <td>{{ saksi.tanggal }}</td>
                <td>
                  <select
                    class="form-control"
                    @change="changeStatus(saksi.id_transaksi, $event)"
                  >
                    <option
                      value="baru"
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
                    <i class="fa-solid fa-circle-info"></i>
                  </a>
                </td>
              </tr>
            </table>
          </div>
        </div>
      </div>
    </main>

    <b-modal
      id="modal-detail"
      ref="modal"
      title="Detail Transaksi"
      size="md"
      hide-footer="true"
    >
      <a href="#" @click="Prints()">print</a>
      <div class="table-responsive table table-stripped" id="print">
        <b-table
          striped
          hover
          :items="detail_transaksi"
          :fields="fields_detail_transaksi"
        >
        </b-table>
        <div class="text-right">
          <h4>Total: Rp{{ total }}</h4>
        </div>
      </div>
    </b-modal>

    <b-modal
      id="modal-recipe"
      ref="modal"
      title="Nota Pemesanan"
      size="md"
      hide-footer="true"
    >
      <div class="table-responsive">
        <b-table
          striped
          hover
          :items="detail_transaksi"
          :fields="fields_detail_transaksi"
        >
        </b-table>
        <div class="text-right">
          <h4>Total: Rp{{ total }}</h4>
        </div>
      </div>
    </b-modal>
  </div>
</template>

<script>
module.exports = {
  data: function () {
    return {
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
        tanggal: this.tanggal,
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
    changeStatus: function (id_transaksi, event) {
      let conf = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };
      let form = {
        id_transaksi: id_transaksi,
        status: event.target.value,
      };
      axios
        .put(base_url + "/transaksi/status", form, conf)
        .then((response) => {
          this.getData();
          Swal.fire(response.data.message);
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
          Swal.fire(response.data.message);
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
      const prtHtml = document.getElementById("print").innerHTML;
      //console.log(prtHtml);
      let stylesHtml = "";
      for (const node of [
        ...document.querySelectorAll('link[rel="stylesheet"], style'),
      ]) {
        stylesHtml += node.outerHTML;
      }
      const WinPrint = window.open(
        "",
        "",
        "left=0,top=0,width=800,height=900,toolbar=0,scrollbars=0,status=0"
      );
      WinPrint.document.write(`<!DOCTYPE html>
        <html>
          <head>
          <link rel="stylesheet" href="src/assets/css/bootstrap.min.css">
            
          </head>
          <body>
            ${prtHtml}
          </body>
        </html>`);
      WinPrint.document.close();
      WinPrint.focus();
      WinPrint.print();
      WinPrint.close();
    },
  },
  mounted() {
    this.getData();
  },
};
</script>
