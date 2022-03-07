<template>
  <div>
    <main>
      <h1 class="h3 mb-0 text-gray-800">User</h1>
      <div class="card mt-4 mb-4">
        <div class="card-body">
          <a
            v-b-modal.modal_user
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
                  <th>Aksi</th>
                </tr>
              </thead>
              <tfoot>
                <th>#</th>
                <th>Nama</th>
                <th>Username</th>
                <th>Role</th>
                <th>Aksi</th>
              </tfoot>
              <tbody>
                <tr v-for="(ket, index) in paket" :key="index">
                  <td>{{ index + 1 }}</td>
                  <td>{{ ket.nama }}</td>
                  <td>{{ ket.username }}</td>
                  <td>{{ ket.role }}</td>
                  <td>
                    <a
                      v-b-modal.modal_user
                      href="#"
                      class="btn bg-gradient-primary text-light mr-2"
                      @click="Edit(ket)"
                      >Ubah</a
                    >
                    <a
                      href="#"
                      class="btn btn-danger"
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
      id="modal_user"
      ref="modal"
      title="Form Paket"
      size="md"
      @ok="Save"
    >
      <form>
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <span class="input-group-text" id="basic-addon1">
              <i class="fas fa-user"></i>
            </span>
          </div>
          <input
            v-model="jenis_paket"
            type="text"
            class="form-control"
            placeholder="Masukkan Jenis Paket"
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
            type="number"
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
  // Initial State
  data: function () {
    return {
      id_outlet: "",
      nama: "",
      username: "",
      password: "",
      role: "",
      user: [],
      outlet: [],
      aksi: "",
    };
  },
  methods: {
    // Pengecekan Token | isHaveCookiesToken?
    getData: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      // Get Data Section | Ditampilkan Ke Tabel Nantinya
      axios.get(base_url + "/paket", config).then((response) => {
        console.log(response);
        if (response.data.success == true) {
          this.paket = response.data.data.paket;
        }
      });
    },

    // Insert Section | To Change Pop Up Action to Insert Method
    Add: function () {
      this.action = "insert";
      this.id_paket = "";
      this.jenis_paket = "";
      this.harga = "";
    },

    // Insert Section | To Change Pop Up Action to Update Method
    Edit: function (item) {
      this.action = "update";
      this.id_paket = item.id_paket;
      this.jenis_paket = item.jenis_paket;
      this.harga = item.harga;
    },

    // Save Method | Used to Button @click
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

      // Post Section
      if (this.action == "insert") {
        axios.post(base_url + "/paket", form, config).then((response) => {
          alert(response.data.message);
        });
      } else {
        // Update Section
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
