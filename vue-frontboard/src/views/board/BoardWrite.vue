<template>
  <div class="board-detail">
    <div class="common-buttons">
      <button type="button" class="w3-button w3-blue-gray" v-on:click="fnSave">
        저장
      </button>
      <button type="button" class="w3-button w3-gray" v-on:click="fnList">
        목록
      </button>
    </div>
    <div class="board-contents">
      번호<input
        type="text"
        v-model="emp_no"
        class="w3-input w3-border"
        placeholder="번호를 입력해주세요"
        :disabled="emp_no !== undefined"
      />
    </div>
    <div class="board-contents">
      <p>
        <input
          class="form-check-input"
          type="radio"
          name="flexRadioDefault"
          id="flexRadioDefault1"
          v-model="gender"
          value="M"
        />
        <label class="form-check-label" for="flexRadioDefault1">
          남자&nbsp;
        </label>
        <input
          class="form-check-input"
          type="radio"
          name="flexRadioDefault"
          id="flexRadioDefault2"
          v-model="gender"
          value="F"
        />
        <label class="form-check-label" for="flexRadioDefault2">
          여자&nbsp;
        </label>
        <input
          type="text"
          v-model="gender"
          class="w3-input w3-border"
          placeholder="성별을 입력해주세요"
        />
      </p>
    </div>

    <div class="board-contents">
      First name<input
        type="text"
        v-model="first_name"
        class="w3-input w3-border"
        placeholder="First name을 입력해주세요"
        :disabled="this.screen_mode === 'update'"
      />
    </div>
    <div class="board-contents">
      Last name<input
        type="text"
        v-model="last_name"
        class="w3-input w3-border"
        placeholder="Last name을 입력해주세요"
        :disabled="this.screen_mode === 'update'"
      />
    </div>
    <div class="board-contents">
      생년월일<input
        type="date"
        v-model="birth_date"
        class="w3-input w3-border"
        placeholder="생년월일을 입력해주세요"
      />
    </div>
    <div class="board-contents">
      입사일<input
        type="date"
        v-model="hire_date"
        class="w3-input w3-border"
        placeholder="입사일을 입력해주세요"
      />
    </div>
    <!-- <div class="board-contents">
      <input
        type="text"
        v-model="title"
        class="w3-input w3-border"
        placeholder="제목을 입력해주세요"
      />
    </div>
    <div class="board-contents">
      <input
        type="text"
        v-model="author"
        class="w3-input w3-border"
        placeholder="작성자를 입력해주세요"
        v-if="idx === undefined"
      />
    </div> -->
    <div class="common-buttons">
      <button type="button" class="w3-button w3-blue-gray" v-on:click="fnSave">
        저장
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
    //변수생성
    return {
      requestBody: this.$route.query,
      emp_no: this.$route.query.emp_no,

      gender: "",
      hire_date: "",
      birth_date: "",
      first_name: "",
      last_name: "",
      screen_mode: "",
    };
  },
  mounted() {
    this.fnGetView();
  },
  methods: {
    fnGetView() {
      if (this.emp_no !== undefined) {
        this.$axios
          .get(this.$serverUrl + "/employee/" + this.emp_no, {
            params: this.requestBody,
          })
          .then((res) => {
            // this.title = res.data.title
            // this.author = res.data.author
            // this.contents = res.data.contents
            // this.create_at = res.data.created_at

            (this.emp_no = res.data.emp_no),
              (this.gender = res.data.gender),
              (this.hire_date = res.data.hire_date),
              (this.birth_date = res.data.birth_date),
              (this.first_name = res.data.first_name),
              (this.last_name = res.data.last_name),
              (this.screen_mode = "update");
          })
          .catch((err) => {
            console.log(err);
          });
      } else if (this.emp_no === undefined) {
        this.$axios
          .get(this.$serverUrl + "/employeeMax", {})
          .then((res) => {
            console.log("this.res" + JSON.stringify(res, null, 2));
            (this.emp_no = res.data.emp_no), (this.screen_mode = "write");
          })
          .catch((err) => {
            console.log(err);
          });
      }
    },
    fnSave() {
      let apiUrl = this.$serverUrl + "/employee";
      this.form = {
        // "idx": this.idx,
        // "title": this.title,
        // "contents": this.contents,
        // "author": this.author
        emp_no: this.emp_no,
        gender: this.gender,
        hire_date: this.hire_date,
        birth_date: this.birth_date,
        first_name: this.first_name,
        last_name: this.last_name,
      };
      console.log("this.form" + JSON.stringify(this.form, null, 2));
      if (this.screen_mode === "write") {
        // if (this.emp_no === undefined || this.emp_no === "") {
        //INSERT
        this.$axios
          .post(apiUrl, this.form)
          .then((res) => {
            alert("글이 저장되었습니다");
            this.fnView(res.data.entity.emp_no);
          })
          .catch((err) => {
            if (err.message.indexOf("Network Error") > -1) {
              alert("네트워크가 원할하지 않습니다/n잠시 후 다시 시도해주세요 ");
            }
          });
      } else {
        //UPDATE
        this.$axios
          .patch(apiUrl, this.form)
          .then((res) => {
            alert("글이 저장되었습니다");
            console.log(res);
            this.fnView(res.data.entity.emp_no);
          })
          .catch((err) => {
            if (err.message.indexOf("Network Error") > -1) {
              alert("네트워크가 원할하지 않습니다/n 잠시 후 다시 시도해주세요");
            }
          });
      }
    },
    fnView(emp_no) {
      this.requestBody.emp_no = emp_no;
      this.$router.push({
        path: "./detail",
        query: this.requestBody,
      });
    },
    fnList() {
      delete this.requestBody.emp_no;
      this.$router.push({
        path: "./list",
        query: this.requestBody,
      });
    },
  },
};
</script>
