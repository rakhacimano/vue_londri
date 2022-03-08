<template>
  <div>
    <main>
      <h1 class="h3 mb-0 text-gray-800">Member</h1>
      <div class="card mt-4 mb-4">
        <div class="card-body">
          <a
            v-b-modal.modal_member
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
                  <th>Jenis Kelamin</th>
                  <th>Telepon</th>
                  <th>Alamat</th>
                  <th>Aksi</th>
                </tr>
              </thead>
              <tfoot>
                <tr>
                  <th>#</th>
                  <th>Nama</th>
                  <th>Jenis Kelamin</th>
                  <th>Telepon</th>
                  <th>Alamat</th>
                  <th>Aksi</th>
                </tr>
              </tfoot>
              <tbody>
                <tr v-for="(mem, index) in member" :key="index">
                  <td>{{ index + 1 }}</td>
                  <td>{{ mem.nama }}</td>
                  <!-- <td>{{ mem.jenis_kelamin }}</td> -->
                  <td class="text-white">
                    <span
                      v-if="mem.jenis_kelamin === 'l'"
                      class="badge bg-gradient-primary"
                      >Laki-Laki</span
                    >
                    <span
                      v-if="mem.jenis_kelamin === 'p'"
                      class="badge bg-danger"
                      >Perempuan</span
                    >
                  </td>
                  <td>{{ mem.telp }}</td>
                  <td>{{ mem.alamat }}</td>
                  <td>
                    <a
                      v-b-modal.modal_member
                      href="#"
                      class="btn btn-info btn-icon-split text-light mr-2"
                      @click="Edit(mem)"
                    >
                      <span class="icon text-white-50">
                        <i class="fas fa-edit"></i>
                      </span>
                      <span class="text">Ubah Data</span>
                    </a>
                    <a
                      href="#"
                      class="btn btn-outline-danger"
                      @click="Delete(mem.id_member)"
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
      id="modal_member"
      ref="modal"
      title="Form Member"
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
              <i class="fas fa-mobile-alt"></i>
            </span>
          </div>
          <input
            v-model="telp"
            type="number"
            class="form-control"
            placeholder="Masukkan Telpon"
            aria-label="Telpon"
            aria-describedby="basic-addon1"
          />
        </div>
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <span class="input-group-text" id="basic-addon1">
              <i class="fas fa-venus-mars"></i>
            </span>
          </div>
          <select v-model="jenis_kelamin" class="form-control">
            <option selected value="">-- Pilih Jenis Kelamin --</option>
            <option value="l">Laki-Laki</option>
            <option value="p">Perempuan</option>
          </select>
        </div>
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <span class="input-group-text" id="basic-addon1">
              <i class="fas fa-map-marker-alt"></i>
            </span>
          </div>
          <textarea
            v-model="alamat"
            type="text"
            class="form-control"
            id="exampleInputNama"
            aria-describedby="namaHelp"
            placeholder="Masukkan Alamat"
          >
          </textarea>
        </div>
      </form>
    </b-modal>
  </div>
</template>
<script>
module.exports = {
  data: function () {
    return {
      id_member: "",
      nama: "",
      jenis_kelamin: "",
      telp: "",
      alamat: "",
      member: [],
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

      axios.get(base_url + "/member", config).then((response) => {
        console.log(response);
        if (response.data.success == true) {
          this.member = response.data.data.member;
        }
      });
    },
    Add: function () {
      this.action = "insert";
      this.id_member = "";
      this.nama = "";
      this.jenis_kelamin = "";
      this.telp = "";
      this.alamat = "";
    },
    Edit: function (item) {
      this.action = "update";
      this.id_member = item.id_member;
      this.nama = item.nama;
      this.jenis_kelamin = item.jenis_kelamin;
      this.telp = item.telp;
      this.alamat = item.alamat;
    },
    Save: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      let form = {
        nama: this.nama,
        alamat: this.alamat,
        jenis_kelamin: this.jenis_kelamin,
        telp: this.telp,
      };

      //logika method post/get (insert /update)
      if (this.action == "insert") {
        axios.post(base_url + "/member", form, config).then((response) => {
          alert(response.data.message);
        });
      } else {
        //update
        axios
          .put(base_url + "/member/" + this.id_member, form, config)
          .then((response) => {
            alert(response.data.message);
          });
      }

      this.getData();
    },
    Delete: function (id) {
      if (confirm("Apakah anda yakin menghapus data member ini?")) {
        let config = {
          headers: {
            Authorization: "Bearer " + this.$cookies.get("Authorization"),
          },
        };

        axios.delete(base_url + "/member/" + id, config).then((response) => {
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
