<template>
  <div>
    <main>
      <div class="d-flex justify-content-between align-items-center">
        <h1 class="h3 mb-0 text-gray-800">User</h1>
        <a
          v-b-modal.modal_user
          href="#"
          class="btn bg-gradient-primary btn-icon-split text-light mr-2"
          @click="Add"
        >
          <span class="icon text-white-50">
            <i class="fas fa-plus"></i>
          </span>
          <span class="text">Tambah</span>
        </a>
      </div>
      <div class="card mt-4 mb-4">
        <div class="card-body">
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
                  <td class="text-light">
                    <!-- {{ ser.role }} -->
                    <span
                      v-if="ser.role === 'admin'"
                      class="badge bg-gradient-primary p-2"
                      >Admin</span
                    >
                    <span
                      v-if="ser.role === 'kasir'"
                      class="badge bg-gradient-warning p-2"
                      >Kasir</span
                    >
                    <span
                      v-if="ser.role === 'owner'"
                      class="badge bg-gradient-success p-2"
                      >Owner</span
                    >
                  </td>
                  <td>{{ ser.outlet.nama_outlet }}</td>
                  <td>
                    <a
                      v-b-modal.modal_user
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
                      @click="Delete(ser.id)"
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
          <b-form-select v-model="role" class="form-control">
            <option value="">--Pilih Role---</option>
            <option value="admin">Admin</option>
            <option value="owner">Owner</option>
            <option value="kasir">Kasir</option>
          </b-form-select>
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
  data: function () {
    return {
      id_outlet: "",
      nama_outlet: "",
      nama: "",
      username: "",
      password: "",
      role: "",
      action: "",
      user: [],
      data_outlet: [],
    };
  },
  methods: {
    getData: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      axios.get(base_url + "/user", config).then((response) => {
        console.log(response);
        if (response.data.success == true) {
          this.user = response.data.data.users;
        }
      });
    },
    OutletDropdown: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };
      axios.get(base_url + "/outlet", config).then((response) => {
        let json_outlet = response.data.data.outlet;
        let list_outlet = [
          {
            value: "",
            text: "--Pilih Outlet--",
          },
        ];
        json_outlet.forEach((element) => {
          list_outlet.push({
            value: element.id_outlet,
            text: element.nama_outlet,
          });
        });
        this.data_outlet = list_outlet;
      });
    },

    Add: function () {
      this.action = "insert";
      this.id_outlet = "";
      this.id = "";
      this.nama = "";
      this.username = "";
      this.password = "";
      this.role = "";
    },
    Edit: function (item) {
      this.action = "update";
      this.id_outlet = item.id_outlet;
      this.id = item.id;
      this.nama = item.nama;
      this.username = item.username;
      this.role = item.role;
    },

    Save: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      let form = {
        id_outlet: this.id_outlet,
        nama: this.nama,
        username: this.username,
        password: this.password,
        nama_outlet: this.nama_outlet,
        role: this.role,
      };
      if (this.action == "insert") {
        axios
          .post(base_url + "/user/register", form, config)
          .then((response) => {
            this.getData();
            alert(response.data.message);
          })
          .catch((error) => {
            console.log(error);
          });
      } else {
        axios
          .put(base_url + "/user/" + this.id, form, config)
          .then((response) => {
            this.getData();
            alert(response.data.message);
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },
    Delete: function (id) {
      if (confirm("Apakah anda yakin menghapus data user ini?")) {
        let config = {
          headers: {
            Authorization: "Bearer " + this.$cookies.get("Authorization"),
          },
        };

        axios.delete(base_url + "/user/" + id, config).then((response) => {
          alert(response.data.message);
        });

        this.getData();
      }
    },
  },
  mounted() {
    this.getData();
    this.OutletDropdown();
  },
};
</script>
