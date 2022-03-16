<template>
  <div>
    <main>
      <h1 class="h3 mb-0 text-gray-800">Transaksi</h1>
      <div class="card mt-4 mb-4">
        <div class="card-body">
          <a
            v-b-modal.modal_transaksi
            href="#"
            class="btn bg-gradient-primary btn-icon-split text-light mr-2 mb-3"
            @click="Add"
          >
            <span class="icon text-white-50">
              <i class="fas fa-plus"></i>
            </span>
            <span class="text">Tambah</span>
          </a>
          <div class="table-responsive">
            <table
              class="table table-bordered"
              id="dataTable"
              width="100%"
              cellspacing="0"
            >
              <thead>
                <tr>
                  <th>#</th>
                  <th>Nama</th>
                  <th>Username</th>
                  <th>Role</th>
                  <th>Nama Outlet</th>
                  <th>Aksi</th>
                </tr>
              </thead>
              <tfoot>
                <tr>
                  <th>#</th>
                  <th>Nama</th>
                  <th>Username</th>
                  <th>Role</th>
                  <th>Nama Outlet</th>
                  <th>Aksi</th>
                </tr>
              </tfoot>
              <tbody>
                <tr v-for="(ser, index) in user" :key="index">
                  <td>{{ index + 1 }}</td>
                  <td>{{ ser.nama }}</td>
                  <td>{{ ser.username }}</td>
                  <td>{{ ser.role }}</td>
                  <td>{{ ser.id_outlet }}</td>
                  <td>
                    <a
                      v-b-modal.modal_transaksi
                      href="#"
                      class="btn btn-info btn-icon-split text-light mr-2"
                      @click="Edit(ser)"
                    >
                      <span class="icon text-white-50">
                        <i class="fas fa-edit"></i>
                      </span>
                      <span class="text">Ubah Data</span>
                    </a>
                    <a
                      href="#"
                      class="btn btn-outline-danger"
                      @click="Delete(tlet.id_user)"
                    >
                      <i class="fas fa-fw fa-trash"></i
                    ></a>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </main>

    <b-modal
      id="modal_transaksi"
      ref="modal"
      title="Form User"
      size="md"
      @ok="Save"
      ok-title="Tambah"
      cancel-title="Batal"
    >
      <form>
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <span class="input-group-text" id="basic-addon1">
              <i class="fas fa-user"></i>
            </span>
          </div>
          <input
            v-model="nama"
            type="text"
            class="form-control"
            placeholder="Masukkan Nama"
            aria-label="Nama"
            aria-describedby="basic-addon1"
          />
        </div>
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <span class="input-group-text" id="basic-addon1">
              <i class="fas fa-user"></i>
            </span>
          </div>
          <input
            v-model="username"
            type="text"
            class="form-control"
            placeholder="Masukkan Username"
            aria-label="Nama"
            aria-describedby="basic-addon1"
          />
        </div>
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <span class="input-group-text" id="basic-addon1">
              <i class="fas fa-key"></i>
            </span>
          </div>
          <input
            v-model="password"
            type="password"
            class="form-control"
            placeholder="Masukkan Password"
            aria-label="Nama"
            aria-describedby="basic-addon1"
          />
        </div>
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <span class="input-group-text" id="basic-addon1">
              <i class="fas fa-user-tag"></i>
            </span>
          </div>
          <select v-model="role" class="form-control">
            <option value="">--Pilih Role---</option>
            <option value="admin">Admin</option>
            <option value="owner">Owner</option>
            <option value="kasir">Kasir</option>
          </select>
        </div>
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <span class="input-group-text" id="basic-addon1">
              <i class="fas fa-store"></i>
            </span>
          </div>
          <b-form-select
            v-model="id_outlet"
            :options="data_outlet"
            class="form-control"
          >
          </b-form-select>
        </div>
      </form>
    </b-modal>
  </div>
</template>
<script>
module.exports = {
  data : function(){
    return {
      tahun: "",
      bulan: "",
      tgl: "",
      action: "",
      message: "",
      key: "",
      total: "",
      transaksi: [],
      detail_transaksi: [],
      fields_transaksi: ["id_transaksi", "nama_member", "tgl", "status", "dibayar", "tgl_bayar", "kasir", "total", "Aksi"],
      fields_detail_transaksi: ["jenis", "berat", "sub_total"],
      list_years: [2020, 2021, 2022, 2023],
      list_months: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
    }
  },
  methods: {
    getData : function(){
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      let form = {
        "tahun": this.tahun,
        "bulan": this.bulan,
        "tgl": this.tgl
      }
      axios.post(base_url + "/transaksi/report", form, conf)
      .then(response => {
          this.transaksi = response.data.data;
      })
      .catch(error => {
        console.log(error);
      });
    },
    changeStatus: function(id_transaksi, event){
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      let form = {
        "id_transaksi": id_transaksi,
        "status": event.target.value
      }
      axios.put(base_url + "/transaksi/status", form, conf)
      .then(response => {
        this.getData();
        this.message = response.data.message;
        this.$bvToast.show("message");
      })
      .catch(error => {
        console.log(error);
      });
    },
    changeBayar: function(id_transaksi, event){
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      let form = {
        "id_transaksi": id_transaksi,
        "status": event.target.value
      }
      axios.put(base_url + "/transaksi/bayar", form, conf)
      .then(response => {
        this.getData();
        this.message = response.data.message;
        this.$bvToast.show("message");
      })
      .catch(error => {
        console.log(error);
      });
    },
    Detail: function(detail_transaksi, total){
      this.total = total;
      this.detail_transaksi = detail_transaksi;
      
    },
    Prints: function(){
      const prtHtml = document.getElementById('print').innerHTML;
      //console.log(prtHtml);
      let stylesHtml = '';
      for (const node of [...document.querySelectorAll('link[rel="stylesheet"], style')]) {
        stylesHtml += node.outerHTML;
      }
      const WinPrint = window.open('', '', 'left=0,top=0,width=800,height=900,toolbar=0,scrollbars=0,status=0');
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
    }
  },
  mounted(){
    this.key = this.$cookies.get("Authorization");
    this.getData();
  }
}
</script>
