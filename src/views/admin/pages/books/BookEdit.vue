<template>
    <div>
        <AppHeader />
        <div v-if="book" class="container mt-4">
            <div class="page row">
                <div class="col-md-8">
                    <h4 class="mb-4 text-center">Hiệu chỉnh đầu sách </h4>
                    <BookForm :book="book" :isEditMode="true" @submit:book="updateBook" @delete:book="deleteBook"
                        class="form-container" />
                    <p v-if="message" class="text-center text-success">{{ message }}</p>
                </div>
                <div class="col-md-4">
                    <div class="d-flex justify-content-center mt-4">
                        <button class="btn btn-primary me-2" @click="updateBook" :disabled="!book">
                            <i class="fas fa-save"></i> Cập nhật
                        </button>
                        <button class="btn btn-danger" @click="deleteBook" :disabled="!book">
                            <i class="fas fa-trash"></i> Xóa
                        </button>
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
import BookForm from '@/components/admin/BookForm.vue';
import BookService from '@/services/admin/book.service';

export default {
    components: {
        AppHeader,
        BookForm,
    },
    props: {
        id: { type: String, required: true },
    },
    data() {
        return {
            book: null,
            message: "",
        };
    },
    methods: {
        async getBook(id) {
            try {
                this.book = await BookService.get(id);
            } catch (error) {
                console.error(error);
                // Chuyển sang trang NotFound đồng thời giữ cho URL không đổi
                this.$router.push({
                    name: "notfound",
                    params: { pathMatch: this.$route.path.split("/").slice(1) },
                    query: this.$route.query,
                    hash: this.$route.hash,
                });
            }
        },
        async updateBook(data) {
            if (confirm("Bạn muốn cập nhật cuốn sách này?")) {
                try {
                    await BookService.update(this.book._id, data);
                    toast.success("Cuốn sách đã được cập nhật thành công!", { autoClose: 1200 });
                    setTimeout(() => {
                        this.$router.push("/admin/books");
                    }, 1200);
                } catch (error) {
                    console.error(error);
                    toast.error("Cập nhật sách thất bại.", { autoClose: 1200 });
                }
            }
        },
        async deleteBook() {
            if (confirm("Bạn muốn xóa sách này?")) {
                try {
                    await BookService.delete(this.book._id);
                    toast.success("Cuốn sách đã bị xóa thành công!", { autoClose: 1200 });
                    setTimeout(() => {
                        this.$router.push("/admin/books");
                    }, 1200);
                } catch (error) {
                    console.log(error);
                    toast.error("Xóa sách không thành công.", { autoClose: 1200 });
                }
            }
        },
    },
    created() {
        this.getBook(this.id);
    },
};
</script>

<style scoped>
.page {
    background-color: #f8f9fa;
    padding: 30px;
    border-radius: 8px;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

h4 {
    font-size: 1.8rem;
    color: #333;
    margin-bottom: 20px;
    text-align: center;
}

p {
    font-size: 1rem;
    color: #28a745;
    margin-top: 15px;
    text-align: center;
}

button {
    padding: 10px 20px;
    font-size: 1rem;
    border-radius: 5px;
    border: none;
    cursor: pointer;
    transition: background-color 0.3s, box-shadow 0.3s;
}

button:focus {
    outline: none;
}

.btn-primary {
    background-color: #007bff;
    color: #fff;
}

.btn-primary:hover {
    background-color: #0056b3;
    box-shadow: 0 4px 6px rgba(0, 123, 255, 0.4);
}

.btn-danger {
    background-color: #dc3545;
    color: #fff;
}

.btn-danger:hover {
    background-color: #c82333;
    box-shadow: 0 4px 6px rgba(220, 53, 69, 0.4);
}

.form-container {
    padding: 30px;
    background-color: #fff;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.form-group {
    margin-bottom: 20px;
}

.form-group label {
    font-weight: bold;
    color: #333;
    font-size: 1.1rem;
}

.form-control {
    width: 100%;
    padding: 10px 15px;
    font-size: 1rem;
    border-radius: 5px;
    border: 1px solid #ccc;
    transition: border-color 0.2s;
}

.form-control:focus {
    border-color: #007bff;
    box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
    outline: none;
}

.error-feedback {
    color: red;
    font-size: 0.875em;
    margin-top: 5px;
}
</style>
