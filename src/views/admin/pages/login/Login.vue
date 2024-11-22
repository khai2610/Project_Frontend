<template>
  <div class="login_page">
    <div class="login-container">
      <div class="left-side">
        <!-- Logo quản lý -->
        <div class="management-logo">
        </div>
      </div>
      <div class="right-side">
        <a href="/admin/auth/login" class="logo-link">
        </a>
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
        </form>
      </div>
    </div>
  </div>
</template>


<script>
import AuthorizationServiceAdmin from "../../../../services/admin/authorization.service";
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
        const response = await AuthorizationServiceAdmin.submitLogin(
          this.formData
        );
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
              this.$router.push({ name: "book" });
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
  /* Đảm bảo trang web chiếm hết chiều cao của màn hình */
  margin: 0;
  /* Loại bỏ margin mặc định của trình duyệt */
}

.login_page {
  display: flex;
  justify-content: flex-end;
  /* Đẩy form đăng nhập sang bên phải */
  align-items: flex-start;
  /* Đảm bảo form đăng nhập ở góc trên cùng */
  height: 100vh;
  /* Chiều cao trang */
  background-image: url('../../../../img/bg3.gif');
  /* Background của trang */
  background-size: cover;
  /* Đảm bảo background phủ toàn bộ trang */
  background-position: center;
  background-attachment: fixed;
  /* Background không di chuyển khi cuộn trang */
}

.login-container {
  display: flex;
  width: 100%;
  max-width: 1000px;
  /* Tăng chiều rộng của form từ 400px thành 500px */
  height: auto;
  margin-top: 30px;
  /* Khoảng cách từ trên xuống */
  margin-right: 10%;
  /* Khoảng cách từ bên phải (tỉ lệ 6-4) */
}

.left-side {
  flex: 3;
  /* Phần bên trái chiếm 60% */
  background-image: url('../../../../img/bg3.gif');
  background-size: cover;
  background-position: center;
  height: 100%;
}

.right-side {
  flex: 2;
  /* Phần bên phải chiếm 40% */
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 10px;
  /* Tăng padding để form rộng hơn */
  background-color: rgba(255, 255, 255, 0.9);
  /* Nền trắng trong suốt */
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  text-align: center;
}

.logo {
  width: 150px;
  margin-bottom: 20px;
}

.login-title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
}

.login-form {
  display: flex;
  flex-direction: column;
}

.form-item {
  margin: 10px 0;
}

.input {
  width: 300px;
  padding: 20px;
  /* Tăng padding để input rộng hơn */
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 14px;
}

.btn {
  padding: 12px;
  /* Tăng padding cho nút bấm */
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
