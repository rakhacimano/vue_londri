<template>
  <div>
    <main>
      <h1 class="h3 mb-0 text-gray-800">Outlet</h1>
      <div class="card mt-4 mb-4">
        <div class="card-body">
          <a
            v-b-modal.modal_outlet
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
                  <th>Nama Outlet</th>
                  <th>Aksi</th>
                </tr>
              </thead>
              <tfoot>
                <tr>
                  <th>#</th>
                  <th>Nama Outlet</th>
                  <th>Aksi</th>
                </tr>
              </tfoot>
              <tbody>
                <tr v-for="(tlet, index) in outlet" :key="index">
                  <td>{{ index + 1 }}</td>
                  <td>{{ tlet.nama_outlet }}</td>
                  <td>
                    <a
                      v-b-modal.modal_outlet
                      href="#"
                      class="btn btn-info btn-icon-split text-light mr-2"
                      @click="Edit(tlet)"
                    >
                      <span class="icon text-white-50">
                        <i class="fas fa-edit"></i>
                      </span>
                      <span class="text">Ubah Data</span>
                    </a>
                    <a
                      href="#"
                      class="btn btn-outline-danger"
                      @click="Delete(tlet.id_outlet)"
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
      id="modal_outlet"
      ref="modal"
      title="Form Outlet"
      size="md"
      @ok="Save"
      ok-title="Tambah"
      cancel-title="Batal"
    >
      <form>
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <span class="input-group-text" id="basic-addon1">
              <i class="fas fa-store"></i>
            </span>
          </div>
          <input
            v-model="nama_outlet"
            type="text"
            class="form-control"
            placeholder="Masukkan Nama Outlet"
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
      id_outlet: "",
      nama_outlet: "",
      outlet: [],
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

      axios.get(base_url + "/outlet", config).then((response) => {
        console.log(response);
        if (response.data.success == true) {
          this.outlet = response.data.data.outlet;
        }
      });
    },
    Add: function () {
      this.action = "insert";
      this.id_outlet = "";
      this.nama_outlet = "";
    },
    Edit: function (item) {
      this.action = "update";
      this.id_outlet = item.id_outlet;
      this.nama_outlet = item.nama_outlet;
    },
    Save: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      let form = {
        nama_outlet: this.nama_outlet,
      };

      //logika method post/get (insert /update)
      if (this.action == "insert") {
        axios.post(base_url + "/outlet", form, config).then((response) => {
          alert(response.data.message);
        });
      } else {
        //update
        axios
          .put(base_url + "/outlet/" + this.id_outlet, form, config)
          .then((response) => {
            alert(response.data.message);
          });
      }

      this.getData();
    },
    Delete: function (id) {
      if (confirm("Apakah anda yakin menghapus data outlet ini?")) {
        let config = {
          headers: {
            Authorization: "Bearer " + this.$cookies.get("Authorization"),
          },
        };

        axios.delete(base_url + "/outlet/" + id, config).then((response) => {
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
