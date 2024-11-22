<template>
  <div>
    <div class="login_page">
      <div class="login-container">
        <!-- Phần trái -->
        <div class="left-side">
          <!-- Bạn có thể thêm logo hoặc hình ảnh khác vào đây -->
        </div>

        <!-- Phần phải chứa form đăng nhập -->
        <div class="right-side">
          <div class="login-title">Đăng nhập</div>
          <form @submit.prevent="login" class="login-form">
            <div class="form-item">
              <input v-model="formData.email" type="text" id="email" class="input"
                placeholder="Email hoặc số điện thoại" />
            </div>
            <div class="form-item">
              <input v-model="formData.password" type="password" id="password" class="input" placeholder="Mật khẩu" />
            </div>
            <button type="submit" class="btn btn-primary">Đăng nhập</button>
            <div class="forgot-password">
              <a href="#">Quên mật khẩu?</a>
            </div>
            <div class="register">
              Bạn chưa có tài khoản?
              <router-link :to="{ name: 'register-client' }">Đăng ký</router-link>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import AuthorizationServiceClient from "../../../../services/client/authorization.service";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";

export default {
  data() {
    return {
      formData: {
        email: "",
        password: "",
      }
    }
  },
  methods: {
    async login() {
      try {
        const response = await AuthorizationServiceClient.submitLogin(this.formData);
        switch (response.data) {
          case "wrong info":
            toast.error("Đăng nhập không thành công. Vui lòng kiểm tra thông tin đăng nhập và thử lại.", {
              autoClose: 800,
            });
            break;
          case "success":
            toast.success("Đăng nhập thành công", {
              autoClose: 800,
            });
            setTimeout(() => {
              this.$router.push({ name: "book-client" });
            }, 1500);
            break;
          default:
            break;
        }
      } catch (error) {
        console.log(error);
        toast.error("Thông tin đăng nhập không chính xác", {
          autoClose: 800,
        });
      }
    }
  }
}
</script>

<style scoped>
html,
body {
  height: 100%;
  margin: 0;
}

.login_page {
  display: flex;
  justify-content: flex-start;
  /* Đảm bảo form đăng nhập nằm về bên phải */
  align-items: center;
  height: 100vh;
  background-image: url('../../../../img/bg3.gif');
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
}

.login-container {
  display: flex;
  width: 100%;
  height: auto;
  margin-right: 10%;
}

.left-side {
  flex: 6;
  /* Chiếm 60% không gian */
  background-image: url('../../../../img/bg3.gif');
  background-size: cover;
  background-position: center;
  height: 100%;
}

.right-side {
  flex: 4;
  /* Chiếm 40% không gian */
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 20px;
  background-color: rgba(255, 255, 255, 0.9);
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  text-align: center;
}

.login-title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
}

.login-form {
  display: flex;
  flex-direction: column;
  width: 100%;
  max-width: 350px;
}

.form-item {
  margin: 10px 0;
}

.input {
  width: 100%;
  padding: 15px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 14px;
}

.btn {
  padding: 12px;
  background-color: #1877f2;
  color: white;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
}

.btn:hover {
  background-color: #165eab;
}

.forgot-password {
  margin-top: 10px;
  text-align: right;
}

.forgot-password a {
  color: #1877f2;
  font-size: 14px;
}

.forgot-password a:hover {
  text-decoration: underline;
}

.register {
  margin-top: 20px;
  font-size: 14px;
}

.register a {
  color: #1877f2;
  font-weight: bold;
}

.register a:hover {
  text-decoration: underline;
}
</style>
