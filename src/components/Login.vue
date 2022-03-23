<template>
  <div class="container-fluid">
    <div class="row justify-content-center">
      <div class="col-xl-9 col-lg-12 col-md-9">
        <div class="card o-hidden border-0 shadow-lg my-5">
          <div class="card-body p-0">
            <!-- Nested Row within Card Body -->
            <div class="row">
              <div class="col-lg-6 d-none d-lg-block bg-login-image"></div>
              <div class="col-lg-6">
                <div class="p-5">
                  <div class="">
                    <h1 class="h4 text-gray-900">Oops. Kami Melupakanmu:(</h1>
                    <p class="mb-4">
                      Login kembali untuk dapat mengakses Londri!
                    </p>
                    <div class="alert alert-info" role="alert">
                      <strong>{{ message }}</strong>
                    </div>
                  </div>
                  <form class="user" @submit.prevent="Login" method="post">
                    <div class="form-group">
                      <input
                        v-model="username"
                        type="text"
                        class="form-control form-control-user"
                        id="exampleInputUsername"
                        aria-describedby="usernameHelp"
                        placeholder="Masukkan Username"
                      />
                    </div>
                    <div class="form-group">
                      <input
                        v-model="password"
                        type="password"
                        class="form-control form-control-user"
                        id="exampleInputPassword"
                        placeholder="Masukkan Password"
                      />
                    </div>
                    <button
                      class="btn btn-primary bg-gradient-primary btn-user btn-block font-weight-bold"
                    >
                      Login
                    </button>
                  </form>
                  <hr />
                </div>
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
      username: "",
      password: "",
      message: "Login Bro",
      success: "",
    };
  },
  methods: {
    Login: function () {
      this.message = "Mohon tunggu..."; //alert notif

      //mapping data
      let form = {
        username: this.username,
        password: this.password,
      };

      axios.post(base_url + "/user/login", form).then((response) => {
        if (response.data.success == true) {
          this.message = response.data.message; //alert

          //cek apakah token sudah ada di cookies???
          if (this.$cookies.isKey("Authorization")) {
            this.$cookies.remove("Authorization");
          }

          //menyimpan token ke cookies
          this.$cookies.set("Authorization", response.data.data.token);

          location.reload();
        } else {
          this.message = response.data.message;
        }
      });
    },
  },
};
</script>
