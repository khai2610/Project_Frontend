<template>
    <div>
        <ClientAppHeader />
        <div class="container mt-3">
            <div class="add-new"><b>Chi Tiết Sách</b></div>
            <!-- <BorrowForm :bookId="$route.params.id" @borrow-success="handleBorrowSuccess" /> -->
            <ClientBookDetail :book="activeBook" />
        </div>
    </div>
</template>

<script>
import ClientAppHeader from "@/components/client/ClientAppHeader.vue";
import BorrowForm from "@/components/client/BorrowForm.vue";
import ClientBookMoreThanDetail from "@/components/client/ClientBookMoreThanDetail.vue";
import ClientBookDetail from "@/components/client/ClientBookDetail.vue";

export default {
    components: {
        ClientAppHeader,
        BorrowForm,
        ClientBookDetail
    },
    methods: {
        handleBorrowSuccess() {
            this.$router.push('/books');
        }
    },
};
</script>

<style scoped>
.container {
    width: 60%;
    height: 670px;
    text-align: center;
    /* margin-top: 10px; */
    background-color: #f5f5f5;
    border: 1px solid #ccc;
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.add-new {
    font-size: 30px;
    margin-top: 10px;
}
</style>
