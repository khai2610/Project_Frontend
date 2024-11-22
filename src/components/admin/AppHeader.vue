<template>
  <div>
    <!-- Header -->
    <div class="header">
      <h1>Manage books, authors, and more efficiently</h1>
    </div>

    <!-- Sidebar -->
    <div :class="['sidebar', { 'sidebar-open': isSidebarOpen }]">
      <div class="sidebar-content">
        <a href="/admin/books" class="logo-text">
          Library<br />Management
        </a>
        <ul class="sidebar-nav">
          <li class="nav-item">
            <router-link :to="{ name: 'book' }" class="nav-link" :class="{ active: $route.name === 'book' }">
              <h6><b>Sách</b></h6>
            </router-link>
          </li>
          <li class="nav-item">
            <router-link :to="{ name: 'borrow' }" class="nav-link" :class="{ active: $route.name === 'borrow' }">
              <h6><b>Đơn Mượn Sách</b></h6>
            </router-link>
          </li>
          <li class="nav-item">
            <router-link :to="{ name: 'reader' }" class="nav-link" :class="{ active: $route.name === 'reader' }">
              <h6><b>Đọc Giả</b></h6>
            </router-link>
          </li>
        </ul>
      </div>

      <!-- Đăng xuất button nằm dưới cùng của Sidebar -->
      <div class="logout-container">
        <button class="btn btn-danger button-logout" @click="logout">
          Đăng xuất
        </button>
      </div>
    </div>

    <!-- Nội dung chính của trang -->
    <div :class="{ 'content-overlay': isSidebarOpen }">
      <!-- Nội dung của trang sẽ được hiển thị tại đây -->
      <slot></slot>
    </div>
  </div>
</template>

<script>
import Authorization from "@/services/admin/authorization.service";
export default {
  data() {
    return {
      isSidebarOpen: true,  // Sidebar mặc định là mở
    };
  },
  methods: {
    async logout() {
      try {
        const response = await Authorization.logOut();
        this.$router.push({ name: "login" });
      } catch (error) {
        console.log(error);
      }
    }
  }
};
</script>

<style scoped>
/* Header styles */
.header {
  background-color: #343a40;
  color: white;
  padding: 20px;
  text-align: center;
}

.header h1 {
  font-size: 24px;
  font-weight: bold;
  margin: 0;
  letter-spacing: 2px;
  text-transform: uppercase;
  font-family: 'Arial', sans-serif;
  /* Cá nhân hóa font chữ cho header */
  color: #f1c40f;
  /* Màu chữ */
}

/* Sidebar styles */
.sidebar {
  position: fixed;
  top: 0;
  left: 0;
  width: 250px;
  height: 100%;
  background-color: #343a40;
  transition: all 0.3s ease;
  z-index: 1000;
  overflow-y: auto;
}

.sidebar-content {
  padding: 20px;
  padding-top: 60px;
  /* Thêm khoảng cách từ trên cùng của sidebar */
}

.logo-text {
  font-size: 20px;
  font-weight: bold;
  color: white;
  text-transform: uppercase;
  letter-spacing: 2px;
  margin-bottom: 20px;
}

.sidebar-nav {
  list-style-type: none;
  padding: 0;
}

.nav-item {
  margin: 10px 0;
}

.nav-link {
  color: white;
  text-decoration: none;
  font-size: 16px;
  transition: background-color 0.2s ease;
  padding: 10px;
  display: block;
}

.nav-link:hover {
  background-color: #007bff;
  color: white;
}

/* Nút đăng xuất ở dưới cùng */
.logout-container {
  position: absolute;
  bottom: 20px;
  width: 100%;
  padding: 10px;
}

.button-logout {
  width: 100%;
  background-color: #dc3545;
  border: none;
  color: white;
  padding: 12px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.button-logout:hover {
  background-color: #c82333;
}

.content-overlay {
  transition: margin-left 0.3s ease;
  margin-left: 250px;
  /* Dịch chuyển nội dung khi mở sidebar */
  padding-left: 20px;
}

.content-overlay.sidebar-open {
  margin-left: 0;
  padding-left: 0;
}
</style>
