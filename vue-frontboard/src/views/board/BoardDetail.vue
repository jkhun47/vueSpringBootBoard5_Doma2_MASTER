<template>
  <div class="board-detail">
    <!-- <table class="w3-table-all">
      <thead>
        <tr>
          <th>번호</th>
          <th>이름</th>
          <th>성별</th>
          <th>생일</th>
          <th>입사일</th>
        </tr>
      </thead>
      <tbody>
        <td>{{ emp_no }}</td>
        <td>{{ first_name }} {{ last_name }}</td>
        <td>{{ gender }}</td>
        <td>{{ birth_date }}</td>
        <td>{{ hire_date }}</td>
      </tbody>
    </table> -->
    <table class="w3-table-all">
      <tbody>
        <tr>
          <th>번호</th>
          <td>{{ emp_no }}</td>
        </tr>
        <tr>
          <th>이름</th>
          <td>{{ first_name }} {{ last_name }}</td>
        </tr>
        <tr>
          <th>성별</th>
          <td>{{ gender }}</td>
        </tr>
        <tr>
          <th>생일</th>
          <td>{{ birth_date }}</td>
        </tr>
        <tr>
          <th>입사일</th>
          <td>{{ hire_date }}</td>
        </tr>
      </tbody>
    </table>
    <div class="common-buttons">
      <button
        type="button"
        class="w3-button w3-round w3-blue-gray"
        v-on:click="fnUpdate"
      >
        수정
      </button>
      <button
        type="button"
        class="w3-button w3-round w3-red"
        v-on:click="fnDelete"
      >
        삭제
      </button>
      <button
        type="button"
        class="w3-button w3-round w3-gray"
        v-on:click="fnList"
      >
        목록
      </button>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      requestBody: this.$route.query,
      emp_no: this.$route.query.emp_no,

      gender: "",
      first_name: "",
      last_name: "",
      birth_date: "",
      hire_date: "",
    };
  },
  mounted() {
    this.fnGetView();
  },
  methods: {
    fnGetView() {
      this.$axios
        .get(this.$serverUrl + "/employee/" + this.emp_no, {
          // params: this.requestBody
        })
        .then((res) => {
          this.gender = res.data.gender;
          this.first_name = res.data.first_name;
          this.last_name = res.data.last_name;
          this.birth_date = res.data.birth_date;
          this.hire_date = res.data.hire_date;
        })
        .catch((err) => {
          if (err.message.indexOf("Network Error") > -1) {
            alert("네트워크가 원할하지 않습니다/n잠시 후 다시 시도해 주세요");
          }
        });
    },
    fnList() {
      delete this.requestBody.idx;
      this.$router.push({
        path: "./list",
        query: this.requestBody,
      });
    },
    fnUpdate() {
      this.$router.push({
        path: "./write",
        query: this.requestBody,
      });
    },
    fnDelete() {
      if (!confirm("삭제하시겠습니까?")) return;

      this.$axios
        .delete(this.$serverUrl + "/employee/" + this.emp_no, {})
        .then(() => {
          alert("삭제되었습니다");
          this.fnList();
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>

<style></style>
