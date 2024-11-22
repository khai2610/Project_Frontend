<template>
    <div>
        <AppHeader />
        <div class="container mt-4">
            <div class="page row">
                <div class="col-md-8">
                    <div class="d-flex justify-content-between align-items-center">
                        <InputSearch v-model="searchText" class="flex-grow-1" />
                        <div class="d-flex">
                            <button class="btn btn-sm btn-outline-primary custom-btn" @click="refreshList">
                                Làm mới
                            </button>
                            <button class="btn btn-sm btn-outline-success custom-btn" @click="goToAddBook">
                                Thêm mới
                            </button>
                            <button class="btn btn-sm btn-outline-danger custom-btn" @click="removeAllBooks">
                                Xóa tất cả
                            </button>
                        </div>
                    </div>
                    <h4 class="text-left">Danh sách sách trong kho</h4>
                    <div v-if="filteredBooksCount > 0" class="book-list">
                        <BookList :books="paginatedBooks" v-model:activeIndex="activeIndex" />
                    </div>
                    <p v-else class="text-center">Không có cuốn sách nào.</p>
                    <div v-if="totalPages > 1" class="pagination">
                        <button class="btn btn-sm btn-outline-secondary" @click="previousPage"
                            :disabled="currentPage === 1">
                            <i class="fas fa-chevron-left"></i>
                        </button>
                        <span class="mx-2">Trang {{ currentPage }} / {{ totalPages }}</span>
                        <button class="btn btn-sm btn-outline-secondary" @click="nextPage"
                            :disabled="currentPage === totalPages">
                            <i class="fas fa-chevron-right"></i>
                        </button>
                    </div>
                </div>
                <div class="col-md-4">
                    <div v-if="activeBook" class="book-detail-card">
                        <h4>Chi tiết đầu sách</h4>
                        <BookDetail :book="activeBook" />
                        <div class="d-flex justify-content-start mt-3">
                            <router-link :to="{
                                name: 'book.edit',
                                params: { id: activeBook._id },
                            }">
                                <span class="badge custom-edit-badge">
                                    Hiệu chỉnh
                                </span>
                            </router-link>
                            <span class="badge custom-delete-badge ms-2" @click="removeOneBook(activeBook)">
                                Xóa
                            </span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import AppHeader from '@/components/admin/AppHeader.vue';
import InputSearch from '@/components/admin/InputSearch.vue';
import BookList from '@/components/admin/BookList.vue';
import BookDetail from '@/components/admin/BookDetail.vue';
import BookService from "@/services/admin/book.service";

export default {
    components: {
        AppHeader,
        InputSearch,
        BookList,
        BookDetail,
    },
    data() {
        return {
            books: [],
            activeIndex: -1,
            searchText: "",
            currentPage: 1,
            itemsPerPage: 5,
        };
    },
    computed: {
        filteredBooks() {
            return this.books.filter(book => book.bookTitle.includes(this.searchText));
        },
        filteredBooksCount() {
            return this.filteredBooks.length;
        },
        totalPages() {
            return Math.ceil(this.filteredBooksCount / this.itemsPerPage);
        },
        paginatedBooks() {
            const start = (this.currentPage - 1) * this.itemsPerPage;
            const end = this.currentPage * this.itemsPerPage;
            return this.filteredBooks.slice(start, end);
        },
        activeBook() {
            return this.activeIndex < 0 ? null : this.paginatedBooks[this.activeIndex];
        }
    },
    methods: {
        async retrieveBooks() {
            try {
                this.books = await BookService.getAll();
            } catch (error) {
                console.log(error);
            }
        },
        refreshList() {
            this.retrieveBooks();
            this.searchText = "";
            this.activeIndex = -1;
            this.currentPage = 1;
        },
        async removeOneBook(book) {
            if (confirm("Bạn muốn xóa cuốn sách này?")) {
                try {
                    await BookService.delete(book._id);
                    toast.success("Sách đã được xóa thành công!", { autoClose: 1200 });
                    this.refreshList();
                } catch (error) {
                    console.log(error);
                    toast.error("Xóa sách không thành công.", { autoClose: 1200 });
                }
            }
        },
        async removeAllBooks() {
            if (confirm("Bạn muốn xóa tất cả sách ?")) {
                try {
                    await BookService.deleteAll();
                    this.refreshList();
                } catch (error) {
                    console.log(error);
                }
            }
        },
        goToAddBook() {
            this.$router.push({ name: "book.add" });
        },
        nextPage() {
            if (this.currentPage < this.totalPages) this.currentPage++;
        },
        previousPage() {
            if (this.currentPage > 1) this.currentPage--;
        }
    },
    mounted() {
        this.refreshList();
    }
}
</script>

<style scoped>
.page {
    text-align: left;
}

.custom-btn {
    margin-left: 10px;
}

.btn-outline-primary {
    border-color: #007bff;
    color: #007bff;
}

.btn-outline-primary:hover {
    background-color: #007bff;
    color: white;
}

.btn-outline-success {
    border-color: #28a745;
    color: #28a745;
}

.btn-outline-success:hover {
    background-color: #28a745;
    color: white;
}

.btn-outline-danger {
    border-color: #dc3545;
    color: #dc3545;
}

.btn-outline-danger:hover {
    background-color: #dc3545;
    color: white;
}

/* Nút hiệu chỉnh */
.custom-edit-badge {
    padding: 10px 20px;
    background-color: #ffc107;
    color: white;
    font-weight: bold;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.custom-edit-badge:hover {
    background-color: #e0a800;
}

/* Nút xóa */
.custom-delete-badge {
    padding: 10px 20px;
    background-color: #dc3545;
    color: white;
    font-weight: bold;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.custom-delete-badge:hover {
    background-color: #c82333;
}

/* Book List */
.book-list {
    background-color: #f4f4f4;
    border-radius: 8px;
    padding: 15px;
}

.book-detail-card {
    background-color: #f8f9fa;
    border-radius: 8px;
    padding: 15px;
}

/* Pagination */
.pagination {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: 20px;
}

.pagination button {
    padding: 5px 15px;
    border: 1px solid #ddd;
    border-radius: 25px;
    background-color: #f8f9fa;
    color: #495057;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    margin: 0 5px;
}

.pagination button:disabled {
    background-color: #e9ecef;
    border-color: #ddd;
    color: #6c757d;
    cursor: not-allowed;
}

.pagination button:hover:not(:disabled) {
    background-color: #007bff;
    color: white;
}

.pagination button:active {
    background-color: #0056b3;
    border-color: #0056b3;
}
</style>
