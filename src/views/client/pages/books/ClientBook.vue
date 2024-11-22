<template>
  <div>
    <ClientAppHeader />
    <div class="container mt-3">
      <div class="page row">
        <!-- Phần tìm kiếm và sắp xếp -->
        <div class="col-md-12">
          <div class="item row mt-3 justify-content-center">
            <div class="col-12 col-md-4">
              <div class="input-group">
                <input type="text" v-model="searchText" class="form-control" placeholder="Nhập từ khóa" />
                <button class="btn btn-primary" @click="searchBooks">
                  <i class="fas fa-search"></i> Tìm kiếm
                </button>
              </div>
            </div>
            <div class="col-12 col-md-3 d-flex align-items-center gap-3 mt-2 mt-md-0">
              <label for="sortOrder" class="mb-0">Sắp xếp theo:</label>
              <select v-model="sortOrder" class="form-control sort-select" @change="sortBooks">
                <option value="none">Giá</option>
                <option value="asc">Tăng dần</option>
                <option value="desc">Giảm dần</option>
              </select>
            </div>
          </div>
        </div>
        <!-- Danh sách sách -->
        <div class="col-md-12 mt-3">
          <ClientBookList v-if="filteredBooksCount > 0" :books="paginatedBooks" v-model:activeIndex="activeIndex" />
          <p v-else class="text-center">Không có sách trong kho.</p>
          <!-- Phân trang -->
          <div class="pagination-container mt-4">
            <button class="btn btn-secondary" :disabled="currentPage === 1" @click="goToPage(currentPage - 1)">
              <i class="fas fa-chevron-left"></i>
            </button>
            <span class="mx-2">Trang {{ currentPage }} / {{ totalPages }}</span>
            <button class="btn btn-secondary" :disabled="currentPage === totalPages" @click="goToPage(currentPage + 1)">
              <i class="fas fa-chevron-right"></i>
            </button>
          </div>
        </div>
        <!-- Overlay -->
        <transition name="fade">
          <div v-if="activeBook" class="overlay-container">
            <div class="overlay" @click="closeOverlay"></div>
            <div class="book-details-popup-horizontal">
              <div class="details-content">
                <h3 class="text-primary">Thông tin sách</h3>
                <ClientBookDetail :book="activeBook" />
                <router-link :to="{ name: 'borrow-book', params: { id: activeBook._id } }">
                  <button class="btn btn-success mt-3">
                   Mượn sách
                  </button>
                </router-link>
              </div>
            </div>
          </div>
        </transition>
      </div>
    </div>
  </div>
</template>



<script>
import ClientBookDetail from "@/components/client/ClientBookDetail.vue";
import ClientAppHeader from "@/components/client/ClientAppHeader.vue";
import ClientBookList from "@/components/client/ClientBookList.vue";
import BookService from "@/services/client/book.service";
import ClientBookMoreThanDetail from "@/components/client/ClientBookMoreThanDetail.vue";

export default {
  components: {
    ClientBookDetail,
    ClientBookList,
    ClientAppHeader,
    
  },
  data() {
    return {
      books: [],
      activeIndex: -1,
      searchText: "",
      currentPage: 1,
      booksPerPage: 10,
      sortOrder: "none",
    };
  },
  watch: {
    searchText() {
      this.activeIndex = -1;
      this.currentPage = 1;
    },
  },
  computed: {
    filteredBooks() {
      if (!this.searchText) return this.books;
      return this.books.filter(book => 
        Object.values(book)
          .join('') 
          .toLowerCase()
          .includes(this.searchText.toLowerCase())
      );
    },
    activeBook() {
      return this.activeIndex >= 0 && this.activeIndex < this.paginatedBooks.length 
        ? this.paginatedBooks[this.activeIndex] 
        : null;
    },
    filteredBooksCount() {
      return this.filteredBooks.length;
    },
    sortedBooks() {
      let booksToDisplay = [...this.filteredBooks];

      if (this.sortOrder === "asc") {
        booksToDisplay = booksToDisplay.sort((a, b) => a.price - b.price);
      } else if (this.sortOrder === "desc") {
        booksToDisplay = booksToDisplay.sort((a, b) => b.price - a.price);
      }

      return booksToDisplay;
    },
    totalPages() {
      return Math.ceil(this.filteredBooksCount / this.booksPerPage);
    },
    paginatedBooks() {
      const startIndex = (this.currentPage - 1) * this.booksPerPage;
      return this.sortedBooks.slice(startIndex, startIndex + this.booksPerPage);
    },
  },
  methods: {
    async retrieveBooks() {
      try {
        this.books = await BookService.getAll();
      } catch (error) {
        console.error("Error retrieving books:", error);
      }
    },
    async refreshList() {
      await this.retrieveBooks();
      this.searchText = "";
      this.activeIndex = -1;
    },
    closeOverlay() {
      this.activeIndex = -1;
    },
    goToAddBook() {
      this.$router.push({ name: "book.add" });
    },
    goToPage(pageNumber) {
      if (pageNumber < 1 || pageNumber > this.totalPages) return;
      this.currentPage = pageNumber;
      this.activeIndex = -1;  // Reset activeIndex khi chuyển trang
    },
    sortBooks() {
      if (this.sortOrder !== "none") {
        this.currentPage = 1;
      }
    },
  },
  mounted() {
    this.refreshList();
  },
};
</script>

<style scoped>
.page {
  text-align: left;
}

.overlay-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1050;
}

.overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  z-index: 1;
  animation: fadeIn 0.3s ease-in-out;
}

.book-details-popup-horizontal {
  position: relative;
  z-index: 2;
  display: flex;
  align-items: center;
  background: #fff;
  padding: 20px;
  border-radius: 10px;
  max-width: 800px;
  width: 90%;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
  animation: slideIn 0.3s ease-out;
}

.book-cover {
  width: 150px;
  height: auto;
  border-radius: 10px;
  margin-right: 20px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.details-content {
  flex: 1;
}

.book-details-popup-horizontal h3 {
  margin-bottom: 20px;
  font-size: 1.5rem;
}

.book-details-popup-horizontal .btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 5px;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}

@keyframes slideIn {
  from {
    transform: translateY(-50px);
    opacity: 0;
  }

  to {
    transform: translateY(0);
    opacity: 1;
  }
}
</style>

