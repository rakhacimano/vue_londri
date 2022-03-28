<template>
  <div id="page-top">
    <div id="wrapper">
      <!-- Sidebar -->
      <ul
        class="navbar-nav bg-gradient-primary sidebar sidebar-dark accordion"
        id="accordionSidebar"
      >
        <!-- Sidebar - Brand -->
        <a
          class="sidebar-brand d-flex align-items-center justify-content-center"
          href="index.html"
        >
          <div class="sidebar-brand-icon">
            <img src="src/assets/img/Favicon.svg" alt="Logo Londri">
          </div>
          <div class="sidebar-brand-text mx-3">Londri</div>
        </a>

        <!-- Divider -->
        <hr class="sidebar-divider my-0" />

        <!-- Nav Item - Dashboard -->
        <li class="nav-item">
          <router-link class="nav-link" to="/">
            <i class="fas fa-archway"></i>
            <span>Dashboard</span>
          </router-link>
        </li>

        <!-- Divider -->
        <hr class="sidebar-divider" />

        <!-- Heading -->
        <div class="sidebar-heading">Interface</div>

        <!-- Nav Item - Pages Collapse Menu -->
        <li class="nav-item">
          <router-link class="nav-link" to="/member" v-if="role !== 'owner'">
            <i class="fas fa-users"></i>
            <span>Member</span>
          </router-link>
        </li>

        <li class="nav-item">
          <router-link class="nav-link" to="/outlet" v-if="role === 'admin'">
            <i class="fas fa-store"></i>
            <span>Outlet</span>
          </router-link>
        </li>

        <li class="nav-item">
          <router-link class="nav-link" to="/paket" v-if="role === 'admin'">
            <i class="fas fa-box"></i>
            <span>Paket</span>
          </router-link>
        </li>

        <li class="nav-item">
          <router-link class="nav-link" to="/user" v-if="role === 'admin'">
            <i class="fas fa-user"></i>
            <span>User</span>
          </router-link>
        </li>
    
        <li class="nav-item">
          <router-link class="nav-link" to="/transaksi">
            <i class="fas fa-info-circle"></i>
            <span>Transaksi</span>
          </router-link>
        </li>
        
      </ul>
      <!-- End of Sidebar -->

      <!-- Content Wrapper -->
      <div id="content-wrapper" class="d-flex flex-column">
        <!-- Main Content -->
        <div id="content">
          <!-- Topbar -->
          <nav
            class="navbar navbar-expand navbar-light bg-white topbar mb-4 static-top shadow"
          >
            
            <a
              class="sidebar-brand d-flex align-items-center justify-content-center text-dark ml-2"
              style="text-decoration: none"
            >
              <div class="sidebar-brand-icon">
                <img class="outlet-logo" src="src/assets/img/Logo-Filled.svg" alt="Logo Londri">
              </div>
              <div class="sidebar-brand-text mx-2">
                <h3 class="h5 mb-0 text-gray-900 font-weight-bold">
                  {{ nama_outlet }}
                </h3>
              </div>
            </a>

            <!-- Topbar Navbar -->
            <ul class="navbar-nav ml-auto">
              
              <!-- Nav Item - User Information -->
              <li class="nav-item dropdown no-arrow">
                <a
                  class="nav-link dropdown-toggle"
                  href="#"
                  id="userDropdown"
                  role="button"
                  data-toggle="dropdown"
                  aria-haspopup="true"
                  aria-expanded="false"
                >
                  <span class="mr-3 text-gray-600 small text-capitalize"
                    >{{ nama }}</span
                  >
                  <img class="rounded" src="https://source.unsplash.com/WNoLnJo7tS8/32x32">
                </a>
                <!-- Dropdown - User Information -->
                <div
                  class="dropdown-menu dropdown-menu-right shadow animated--grow-in"
                  aria-labelledby="userDropdown"
                >
                  <a
                    class="dropdown-item"
                    href="#"
                    data-toggle="modal"
                    data-target="#logoutModal"
                  >
                    <i
                      class="fas fa-sign-out-alt fa-sm fa-fw mr-2 text-gray-400"
                    ></i>
                    Logout
                  </a>
                </div>
              </li>
            </ul>
          </nav>
          <!-- End of Topbar -->

          <!-- Begin Page Content -->
          <div class="container-fluid">
            <!-- Page Heading -->
            <router-view></router-view>
          <!-- /.container-fluid -->
        </div>
        <!-- End of Main Content -->

        
      </div>
      <!-- Footer -->
        <footer class="sticky-footer bg-white">
          <div class="container my-auto">
            <div class="copyright text-center my-auto">
              <span>Template Ini &copy; Digunakan Rakha</span>
            </div>
          </div>
        </footer>
        <!-- End of Footer -->
      <!-- End of Content Wrapper -->
    </div>
    <!-- End of Page Wrapper -->

    <!-- Scroll to Top Button-->
    <a class="scroll-to-top rounded" href="#page-top">
      <i class="fas fa-angle-up"></i>
    </a>

    <!-- Logout Modal-->
    <div
      class="modal fade"
      id="logoutModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title font-weight-bold" id="exampleModalLabel">Yakin mau logout?</h5>
            <button
              class="close"
              type="button"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">×</span>
            </button>
          </div>
          <div class="modal-body">
            Tekan tombol logout jika kamu yakin!
          </div>
          <div class="modal-footer">
            <button
              class="btn btn-outline-secondary"
              type="button"
              data-dismiss="modal"
            >
              Batal
            </button>
            <a @click="Logout" class="btn btn-primary" href="#">Logout</a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
module.exports = {
  data: function () {
    return {
      username: "",
      nama: "",
      nama_outlet: "",
      role: "",
      id_outlet: "",
    };
  },
  methods: {
    getInfo: function () {
      let config = {
        headers: {
          Authorization: "Bearer " + this.$cookies.get("Authorization"),
        },
      };

      axios.get(base_url + "/user/login/check", config).then((response) => {
        if (response.data.success == true) {
          this.username = response.data.data.username;
          this.nama = response.data.data.nama;
          this.role = response.data.data.role;

          // This axios refers to get nama_outlet in Outlet Table
          axios.get(base_url + "/outlet/" + response.data.data.id_outlet, config)
          .then(response2 => {
            if(response2.data.success == true){
              this.nama_outlet = response2.data.data.nama_outlet;
            }
          })
          .catch(error => {
            console.log(error);
          });
        }
      });
      
    },

    // GET DATA OUTLET
    getDataOutlet: function () {
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

    // GET DATA MEMBER
    getDataMember: function () {
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

    // GET DATA PAKET
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

    Logout: function () {
      this.$cookies.remove("Authorization");
      this.componentName = "login";

      document.location.href = "/vue_londri";
    },
  },
  mounted() {
    this.getInfo();
  },
};
</script>
