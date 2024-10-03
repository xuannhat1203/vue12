<template>
  <div>
    <div class="w-[80%] m-auto mt-4 h-[100vh]">
      <main class="main">
        <header class="d-flex justify-content-between mb-3">
          <h3>Nhân viên</h3>
          <button @click="handleOpenAdd" class="btn btn-primary">
            Thêm thông tin
          </button>
        </header>
        <div class="d-flex align-items-center justify-content-end gap-2 mb-3">
          <select @click="handleFilter" v-model="filterItem">
            <option value="">Lọc theo trạng thái</option>
            <option :value="true">Đã trả</option>
            <option :value="false">Chưa trả</option>
          </select>
        </div>
        <!-- Danh sách Book -->
        <table class="table table-bordered table-hover table-striped">
          <thead>
            <tr>
              <th style="text-align: center">STT</th>
              <th style="text-align: center">Tên sách</th>
              <th style="text-align: center">Sinh viên mượn</th>
              <th style="text-align: center">Ngày mượn</th>
              <th style="text-align: center">Ngày trả</th>
              <th style="text-align: center">Trạng thái</th>
              <th style="text-align: center" colspan="3">Chức năng</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(book, index) in filteredBooks" :key="index">
              <td>{{ index + 1 }}</td>
              <td>{{ book.tenSach }}</td>
              <td>{{ book.sinhVienMuon }}</td>
              <td>{{ book.ngayMuon }}</td>
              <td>{{ book.ngayTra }}</td>
              <td>
                <span
                  @click="toggleBlockUser(book)"
                  class="button button-block"
                >
                  {{ book.trangThai ? "Đã trả" : "Chưa trả" }}
                </span>
              </td>
              <td>
                <span class="button button-edit">Sửa</span>
              </td>
              <td @click="handleDelete(book.stt)">
                <span class="button button-delete">Xóa</span>
              </td>
            </tr>
          </tbody>
        </table>
        <footer class="d-flex justify-content-end align-items-center gap-3">
          <select class="form-select">
            <option selected>Hiển thị 10 bản ghi trên trang</option>
            <option>Hiển thị 20 bản ghi trên trang</option>
            <option>Hiển thị 50 bản ghi trên trang</option>
            <option>Hiển thị 100 bản ghi trên trang</option>
          </select>
          <ul class="pagination">
            <li class="page-item">
              <a class="page-link" href="#">Previous</a>
            </li>
            <li class="page-item"><a class="page-link" href="#">1</a></li>
            <li class="page-item"><a class="page-link" href="#">2</a></li>
            <li class="page-item"><a class="page-link" href="#">3</a></li>
            <li class="page-item">
              <a class="page-link" href="#">Next</a>
            </li>
          </ul>
        </footer>
      </main>
    </div>
    <!-- Form thêm mới nhân viên -->
    <div v-if="openModelAdd" class="overlay">
      <form class="form" @submit.prevent="handleAddUser">
        <div class="d-flex justify-content-between align-items-center">
          <h4>Thêm mới nhân viên</h4>
          <i @click="handleCloseAdd" class="fa-solid fa-xmark"></i>
        </div>
        <div>
          <label class="form-label" for="bookName">Tên sách</label>
          <input
            v-model="newInfor.tenSach"
            id="bookName"
            type="text"
            class="form-control"
            required
          />
        </div>
        <div>
          <label class="form-label" for="borrowerName">Tên người mượn</label>
          <input
            v-model="newInfor.sinhVienMuon"
            id="borrowerName"
            type="text"
            class="form-control"
            required
          />
        </div>
        <div>
          <label class="form-label" for="borrowDate">Ngày mượn</label>
          <input
            v-model="newInfor.ngayMuon"
            id="borrowDate"
            type="date"
            class="form-control"
            required
          />
        </div>
        <div>
          <label class="form-label" for="returnDate">Ngày trả</label>
          <input
            v-model="newInfor.ngayTra"
            id="returnDate"
            type="date"
            class="form-control"
            required
          />
        </div>

        <div>
          <button type="submit" class="w-100 btn btn-primary">Thêm mới</button>
        </div>
      </form>
    </div>
    <!-- xóa -->
    <div v-if="openModelDelete" class="overlay">
      <div class="modal-custom">
        <div class="modal-title">
          <h4>Cảnh báo</h4>
          <i @click="cancelDelete" class="fa-solid fa-xmark"></i>
        </div>
        <div class="modal-body-custom">
          <span>Bạn có chắc chắn muốn xóa tài khoản này?</span>
        </div>
        <div class="modal-footer-custom">
          <button @click="cancelDelete" class="btn btn-light">Hủy</button>
          <button @click="comfirmDelete" class="btn btn-danger">
            Xác nhận
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from "vue";
// const bookLendingData = [
//   {
//     stt: 1,
//     tenSach: "Harry Potter và Hòn Đá Phù Thủy",
//     sinhVienMuon: "Nguyễn Văn A",
//     ngayMuon: "10/04/2024",
//     ngayTra: "17/04/2024",
//     trangThai:true,
//   },
//   {
//     stt: 2,
//     tenSach: "Harry Potter và Hòn Đá Phù Thủy",
//     sinhVienMuon: "Nguyễn Văn A",
//     ngayMuon: "10/04/2024",
//     ngayTra: "17/04/2024",
//     trangThai:true,
//   },
//   {
//     stt: 3,
//     tenSach: "Harry Potter và Hòn Đá Phù Thủy",
//     sinhVienMuon: "Nguyễn Văn A",
//     ngayMuon: "10/04/2024",
//     ngayTra: "17/04/2024",
//     trangThai:false,
//   },
//   {
//     stt: 4,
//     tenSach: "Harry Potter và Hòn Đá Phù Thủy",
//     sinhVienMuon: "Nguyễn Văn A",
//     ngayMuon: "10/04/2024",
//     ngayTra: "17/04/2024",
//     trangThai:true,
//   },
//   {
//     stt: 5,
//     tenSach: "Harry Potter và Hòn Đá Phù Thủy",
//     sinhVienMuon: "Nguyễn Văn A",
//     ngayMuon: "10/04/2024",
//     ngayTra: "17/04/2024",
//     trangThai:false,
//   }
// ];
// localStorage.setItem("listBooks",JSON.stringify(bookLendingData));
const openModelAdd = ref(false);
const openModelDelete = ref(false);
const filterItem = ref(null);
const handleFilter = () => {
  console.log(filterItem.value, 12939);
};
const idUser = ref(null);
const handleDelete = (id) => {
  openModelDelete.value = true;
  idUser.value = id;
};
const cancelDelete = () => {
  openModelDelete.value = false;
};
const listBook = ref(JSON.parse(localStorage.getItem("listBooks")) || []);
const filteredBooks = computed(() => {
  if (filterItem.value === null || filterItem.value === "") {
    return listBook.value;
  }
  return listBook.value.filter((book) => book.trangThai === filterItem.value);
});

const newInfor = ref({
  tenSach: "",
  sinhVienMuon: "",
  ngayMuon: "",
  ngayTra: "",
  trangThai: false,
});
const comfirmDelete = () => {
  const find = listBook.value.filter((book) => book.stt !== idUser.value);
  localStorage.setItem("listBooks", JSON.stringify(find));
  listBook.value = find;
  openModelDelete.value = false;
};

const handleOpenAdd = () => {
  openModelAdd.value = true;
};

const handleCloseAdd = () => {
  openModelAdd.value = false;
  resetNewUser();
};

const handleAddUser = () => {
  const bookExists = listBook.value.find(
    (book) =>
      book.tenSach === newInfor.value.tenSach &&
      book.sinhVienMuon === newInfor.value.sinhVienMuon
  );
  if (!bookExists) {
    const book = {
      id: listBook.value.length + 1,
      ...newInfor.value,
      active: true,
    };
    listBook.value.push(book);
    localStorage.setItem("listBooks", JSON.stringify(listBook.value));
    handleCloseAdd();
  } else {
    alert("Sách này đã được mượn.");
  }
};

const resetNewUser = () => {
  newInfor.value = {
    tenSach: "",
    sinhVienMuon: "",
    ngayMuon: "",
    ngayTra: "",
    trangThai: false,
  };
};

const toggleBlockUser = (book) => {
  book.trangThai = !book.trangThai;
  localStorage.setItem("listBooks", JSON.stringify(listBook.value));
};
</script>

<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

body {
  font-size: 14px;
  font-family: sans-serif;
}

.main {
  margin: 50px 100px;
}

.fa-arrows-rotate {
  font-size: 20px;
  cursor: pointer;
  color: gray;
}

.fa-arrows-rotate:hover {
  color: rgb(184, 180, 180);
  transform: rotate(20deg);
  transition: all 0.5s linear;
}

.button {
  padding: 4px 12px;
  line-height: 30px;
  border-radius: 4px;
  color: #fff;
  cursor: pointer;
}

.button-edit {
  background-color: orange;
}

.button-delete {
  background-color: red;
}

.button-block {
  background-color: green;
}

td:first-child,
td:nth-child(6),
td:nth-child(7) {
  text-align: center;
}

.form-select {
  width: 270px !important;
}

.overlay {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

.form {
  background-color: #fff;
  width: 500px;
  padding: 20px 24px;
  border-radius: 4px;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.form-label {
  font-weight: 600;
  margin-bottom: 8px;
}

.fa-xmark {
  font-size: 20px;
  cursor: pointer;
}

.error {
  color: red !important;
}

.status-container {
  border: 1px solid #dadada;
  padding: 4px 8px;
  border-radius: 4px;
}

.status {
  height: 10px;
  width: 10px;
  border-radius: 50%;
  border: 1px solid transparent;
}

.status-active {
  background-color: green;
}

.status-stop {
  background-color: red;
}

.modal-custom {
  background-color: #fff;
  padding: 20px 24px;
  border-radius: 4px;
  width: 400px;
}

.modal-title {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.modal-body-custom {
  padding: 20px 0;
}

.modal-footer-custom {
  display: flex;
  justify-content: end;
  gap: 8px;
}

.pagination {
  margin-bottom: 0;
}
</style>
