    <template>
  <el-row :gutter="10" class="mt-5 p-3" justify="space-around" type="flex">
    <el-col class="hidden-sm-and-down">
      <img class="animated fadeInRight" src="@/assets/img/logindesing.svg" width="550px" alt>
    </el-col>
    <el-col>
      <el-row type="flex" justify="center">
        <el-col :xs="40" :sm="20" :md="20" :lg="16" :xl="40">
          <el-card class="box-card p-4 card">
            <el-form class="form" :model="ruleForm" :rules="rules" ref="ruleForm" @keyup.enter="submitForm('ruleForm')">
              <el-form-item>
                <el-row>
                  <img src="@/assets/img/lock.svg" width="70px" alt>
                </el-row>
                <el-row>
                  <span class="spam-title">Iniciar Sesión</span>
                </el-row>
              </el-form-item>
              <el-row type="flex" justify="center">
                <el-col>
                  <el-form-item prop="email">
                    <el-input
                      v-model="ruleForm.email"
                      placeholder="Email"
                      
                      size="medium"
                      type="email"
                    ></el-input>
                  </el-form-item>
                  <el-form-item prop="password">
                    <input v-model="ruleForm.password"
                           type="password"
                           placeholder="Contraseña"
                           class="pass" 
                    @keyup.enter="submitForm('ruleForm')">
                  </el-form-item>
                  <el-form-item>
                    <el-button
                      type="primary"
                      v-loading="loading"
                      class="btn btn-block btn-login"
                      @click="submitForm('ruleForm')"
                    >Entrar</el-button>
                  </el-form-item>
                </el-col>
              </el-row>

              <span
                @click="redirect('/registro')"
                class="span-register"
              >¿No tienes cuenta? Click aquí.</span>
              <br>
              <span
                @click="dialogFormVisible = true"
                class="span-register"
              >¿Haz olvidado tu contraseña?</span>
              
            </el-form>
            <el-dialog title="Olvide mi contraseña" :visible.sync="dialogFormVisible">
              <span slot="footer" class="dialog-footer">
                <el-form :model="recoveryPass" :rules="rulesRecovery" ref="recoveryPass">
                  <el-form-item prop="email">
                    <el-input
                      v-model="recoveryPass.email"
                      type="email"
                      placeholder="Ingrese su email"
                      prefix-icon="el-icon-message"
                      size="medium"
                    ></el-input>
                  </el-form-item>
                  <el-form-item>
                    <el-button @click="dialogFormVisible = false">Cancelar</el-button>
                    <el-button class="btn-modal" type="primary" @click="password() , dialogFormVisible = true">Confirmar</el-button>
                  </el-form-item>
                </el-form>
              </span>
            </el-dialog>
          </el-card>
        </el-col>
      </el-row>
    </el-col>
  </el-row>
</template>

<script>
export default {
  data() {
    return {
      loading: false,
      dialogFormVisible: false,
      ruleForm: {
        email: null,
        password: null
      },
      recoveryPass: {
        email: null
      },
      rulesRecovery: {
        email: [
          {
            type: "email",
            required: true,
            message: "Ingrese la direccion email.",
            trigger: "blur"
          }
        ]
      },
      rules: {
        email: [
          {
            type: "email",
            required: true,
            message: "Ingrese la direccion email.",
            trigger: "blur"
          }
        ],
        password: [
          { required: true, message: "Ingrese la contraseña", trigger: "blur" }
        ]
      },
      token: localStorage.getItem("access_token") || null,
      user: localStorage.getItem("user_info") || null,
      idUser: localStorage.getItem("user_id") || null,
      role: localStorage.getItem("user_role") || null,
      email: localStorage.getItem("user_email") || null
    };
  },
  mounted() {
    if (localStorage.getItem("user_id")) {
      this.$router.push("/inicio");
    }
  },
  methods: {
    redirect(path) {
      if (path === undefined) return;
      this.$router.push(path);
    },
    submitForm(formName) {
      let self = this;
      self.$refs[formName].validate(valid => {
        if (valid) {
          self.loading = true;
          self.$store.state.services.authService
            .login(self.ruleForm)
            .then(r => {
              //obtenemos el token
              self.token = r.data.token;
              self.user = r.data.user.name;
              self.idUser = r.data.user.id;
              self.role = r.data.user.role;
              self.email = r.data.user.email;
              //guardamos en el localStorage
              //accedemos al state y le asignamos el token
              self.$store.state.token = localStorage.setItem(
                "access_token",
                self.token
              );
              self.$store.state.user = localStorage.setItem(
                "user_info",
                self.user
              );
              self.$store.state.idUser = localStorage.setItem(
                "user_id",
                self.idUser
              );
              self.$store.state.role = localStorage.setItem(
                "user_role",
                self.role
              );
              self.$store.state.userEmail = localStorage.setItem(
                "user_email",
                self.email
              );
              self.loading = false;
            })
            .then(r => {
              if (self.role == 1) {
                self.redirect("/inicio");
              } else if (self.role == 2) {
                self.redirect("/dashboardAdmin");
              }
            })
            .catch(e => {
              self.$notify.error({
                title: "Error",
                message: "Porfavor revise sus credenciales",
                offset: 40
              });
              self.loading = false;
            });
        } else {
          return false;
        }
      });
    },
    password() {
      let self = this;
      self.$refs["recoveryPass"].validate(valid => {
        if (valid) {
          self.$store.state.services.accountService
            .recoveryPass(this.recoveryPass)
            .then(r => {
              self.$notify.success({
                title: "Enviado",
                message: `Correo enviado`,
                offset: 40
              });
            })
            .catch(e => {
              self.$notify.error({
                title: "Error",
                message: `${e.response.data}`,
                offset: 40
              });
            });
        } else {
          return false;
        }
      });
    }
  }
};
</script>

<style scoped>
.errspan {
        float: right;
        margin-right: 6px;
        margin-top: -20px;
        position: relative;
        z-index: 2;
        color: red;
    }
.spam-title {
  font-family: Roboto;
}
.btn-login {
  background-color: #5a75e6;
  color: white;

  outline: none;
}
.btn-login:hover {
  background-color:#4867e4;
  color: white;
}
.btn-modal {
  background-color: #5a75e6;
}
.btn-modal:hover {
  background-color: #4867e4;
}
.span-register {
  color: #5a75e6;
  cursor: pointer;
  font-size: 13px;
}
.btn-fb {
  background-color: #475a93;
  color: white;
}
.btn-gl {
  background-color: #d62d20;
  color: white;
}
.span-img {
  cursor: pointer;
}
.font {
  background: #f7f7f7;
}
.pass{
   -webkit-appearance: none;
  background-color: #fff;
  background-image: none;
  border-radius: 4px;
  border: 1px solid #dcdfe6;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  color: #606266;
  display: inline-block;
  font-size: 14px;
  height: 38px;
  line-height: 40px;
  outline: 0;
  padding: 0 15px;
  -webkit-transition: border-color 0.2s cubic-bezier(0.645, 0.045, 0.355, 1);
  transition: border-color 0.2s cubic-bezier(0.645, 0.045, 0.355, 1);
  width: 100%;
}
.pass::-webkit-input-placeholder {
  color: #c0c4cc;
}
</style>
