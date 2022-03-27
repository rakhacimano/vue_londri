<template>
  <div>
    <main>
      <h1 class="h3 mb-0 text-gray-800">Paket</h1>
      <div class="card mt-4 mb-4">
        <div class="card-body">
          <!-- Cause Enum Column for Jenis Paket, Add Button Disabled -->
          <a
            v-b-modal.modal_paket
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
                  <th>Jenis Paket</th>
                  <th>Harga</th>
                  <th>Aksi</th>
                </tr>
              </thead>
              <tfoot>
                <tr>
                  <th>#</th>
                  <th>Jenis Paket</th>
                  <th>Harga</th>
                  <th>Aksi</th>
                </tr>
              </tfoot>
              <tbody>
                <tr v-for="(ket, index) in paket" :key="index">
                  <td>{{ index + 1 }}</td>
                  <td>
                    <span v-if="ket.jenis_paket === 'kiloan'">Kiloan</span>
                    <span v-if="ket.jenis_paket === 'kaos'">Kaos</span>
                    <span v-if="ket.jenis_paket === 'boneka'">Boneka</span>
                    <span v-if="ket.jenis_paket === 'bed_cover'">Bed Cover</span>
                  </td>
                  <td>{{ ket.harga }}</td>
                  <td>
                    <a
                      v-b-modal.modal_paket
                      href="#"
                      class="btn btn-info btn-icon-split text-light mr-2"
                      @click="Edit(ket)"
                    >
                      <span class="icon text-white-50">
                        <i class="fas fa-edit"></i>
                      </span>
                      <span class="text">Ubah Data</span>
                    </a>
                    <a
                      href="#"
                      class="btn btn-outline-danger"
                      @click="Delete(ket.id_paket)"
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
      id="modal_paket"
      ref="modal"
      title="Form Paket"
      size="md"
      @ok="Save"
      ok-title="Tambah"
      cancel-title="Batal"
    >
      <form>
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <span class="input-group-text" id="basic-addon1">
              <i class="fas fa-box"></i>
            </span>
          </div>
          <input
            v-model="jenis_paket"
            type="text"
            class="form-control"
            placeholder="Masukkan Nama Paket"
            aria-label="Nama"
            aria-describedby="basic-addon1"
          />
        </div>
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <span class="input-group-text" id="basic-addon1">
              <i class="fas fa-tag"></i>
            </span>
          </div>
          <input
            v-model="harga"
            type="text"
            class="form-control"
            placeholder="Masukkan Harga"
            aria-label="Nama"
            aria-describedby="basic-addon1"
          />
        </div>
      </form>
    </b-modal>

  </div>
</template>
<script>
module.exports = {
  data: function () {
    return {
      id_paket: "",
      jenis_paket: "",
      harga: "",
      paket: [],
      aksi: "",
    };
  },
  methods: {
    getData: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      axios.get(base_url + "/paket", config).then((response) => {
        console.log(response);
        if (response.data.success == true) {
          this.paket = response.data.data.paket;
        }
      });
    },
    Add: function () {
      this.action = "insert";
      this.id_paket = "";
      this.jenis_paket = "";
      this.harga = "";
    },
    Edit: function (item) {
      this.action = "update";
      this.id_paket = item.id_paket;
      this.jenis_paket = item.jenis_paket;
      this.harga = item.harga;
    },
    Save: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      let form = {
        jenis_paket: this.jenis_paket,
        harga: this.harga,
      };

      //logika method post/get (insert /update)
      if (this.action == "insert") {
        axios.post(base_url + "/paket", form, config).then((response) => {
          alert(response.data.message);
        });
      } else {
        //update
        axios
          .put(base_url + "/paket/" + this.id_paket, form, config)
          .then((response) => {
            alert(response.data.message);
          });
      }

      this.getData();
    },
    Delete: function (id) {
      if (confirm("Apakah anda yakin menghapus data paket ini?")) {
        let config = {
          headers: {
            Authorization: "Bearer " + this.$cookies.get("Authorization"),
          },
        };

        axios.delete(base_url + "/paket/" + id, config).then((response) => {
          alert(response.data.message);
        });

        this.getData();
      }
    },
  },
  mounted() {
    this.getData();
  },
};
</script>
