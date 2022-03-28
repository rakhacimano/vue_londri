<template>
  <div>
    <div class="d-sm-flex align-items-center justify-content-between">
      <h1 class="h3 mb-0 text-gray-800">Dashboard</h1>
    </div>
    <div class="row mt-4">
      <div class="col-xl-3 col-md-6 mb-4">
        <div class="card border-left-primary shadow h-100 py-2">
          <div class="card-body">
            <div class="row no-gutters align-items-center">
              <div class="col mr-2">
                <div
                  class="text-xs font-weight-bold text-primary text-uppercase mb-1"
                >
                  Data Member
                </div>
                <div class="h5 mb-0 font-weight-bold text-gray-800">
                  {{ member.length }} Member
                </div>
              </div>
              <div class="col-auto">
                <i class="fas fa-users fa-2x text-gray-300"></i>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="col-xl-3 col-md-6 mb-4">
        <div class="card border-left-success shadow h-100 py-2">
          <div class="card-body">
            <div class="row no-gutters align-items-center">
              <div class="col mr-2">
                <div
                  class="text-xs font-weight-bold text-success text-uppercase mb-1"
                >
                  Data Outlet
                </div>
                <div class="h5 mb-0 font-weight-bold text-gray-800">
                  {{ outlet.length }} Outlet
                </div>
              </div>
              <div class="col-auto">
                <i class="fas fa-store fa-2x text-gray-300"></i>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="col-xl-3 col-md-6 mb-4">
        <div class="card border-left-info shadow h-100 py-2">
          <div class="card-body">
            <div class="row no-gutters align-items-center">
              <div class="col mr-2">
                <div
                  class="text-xs font-weight-bold text-info text-uppercase mb-1"
                >
                  Data Paket
                </div>
                <div class="h5 mb-0 font-weight-bold text-gray-800">
                  {{ paket.length }} Paket
                </div>
              </div>
              <div class="col-auto">
                <i class="fas fa-box fa-2x text-gray-300"></i>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="col-xl-3 col-md-6 mb-4">
        <div class="card border-left-warning shadow h-100 py-2">
          <div class="card-body">
            <div class="row no-gutters align-items-center">
              <div class="col mr-2">
                <div
                  class="text-xs font-weight-bold text-warning text-uppercase mb-1"
                >
                  Data User
                </div>
                <div class="h5 mb-0 font-weight-bold text-gray-800">
                  {{ user.length }} Pengguna
                </div>
              </div>
              <div class="col-auto">
                <i class="fas fa-user fa-2x text-gray-300"></i>
              </div>
            </div>
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
      member: [],
      paket: [],
      outlet: [],
      user: [],
    };
  },

  methods: {
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
    getDataPaket: function () {
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

    getDataUser: function () {
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
  },

  mounted() {
    this.getDataMember();
    this.getDataPaket();
    this.getDataOutlet();
    this.getDataUser();
  },
};
</script>
