<template>
  <div class="board-list">
    <div class="common-buttons">
      <button
        type="button"
        class="w3-button w3-round w3-blue-gray"
        v-on:click="fnWrite"
      >
        추가
      </button>
    </div>
    <div class="w3-table-all">
      <div class="w3-row">
        <div class="w3-cell-row">
          <div class="w3-container w3-cell w3-left-align">
            <p>
              번호&nbsp;<input
                id="empNo"
                v-model.trim="empNo"
                placeholder="번호"
              />
            </p>
          </div>

          <div class="w3-container w3-cell w3-left-align">
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
            </p>
          </div>
        </div>
      </div>
      <div class="w3-cell-row">
        <div class="w3-container w3-cell w3-left-align">
          <p>
            입사일<input
              id="hireDateFrom"
              type="date"
              v-model="hireDateFrom"
              placeholder="입사일(From)"
            />～<input
              id="hireDateTo"
              type="date"
              v-model="hireDateTo"
              placeholder="입사일(From)"
            />
            &nbsp; 생년월일<input
              id="birthDateFrom"
              type="date"
              v-model="birthDateFrom"
              placeholder="생년월일(From)"
            />～<input
              id="birthDateTo"
              type="date"
              v-model="birthDateTo"
              placeholder="생년월일(From)"
            />
          </p>
        </div>
      </div>
      <div class="w3-cell-row w3-left-align">
        <div class="w3-container w3-cell">
          <p>
            <input
              id="customerName"
              v-model.trim="firstName"
              placeholder="FIRST NAME"
            />
            &nbsp;
            <input
              id="productName"
              v-model.trim="lastName"
              placeholder="LAST NAME"
            />
          </p>
        </div>
      </div>
      <div class="w3-right-left">
        <button
          class="w3-button w3-round w3-pale-green w3-hover-green"
          @click="fnPage()"
        >
          검색
        </button>
      </div>
    </div>
    <br />
    <table class="w3-table-all">
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
        <tr v-for="(row, emp_no) in list" :key="emp_no">
          <td>
            <a v-on:click="fnView(`${row.emp_no}`)">{{ row.emp_no }}</a>
          </td>
          <td>{{ row.first_name }} {{ row.last_name }}</td>
          <td>{{ row.gender }}</td>
          <td>{{ row.birth_date }}</td>
          <td>{{ row.hire_date }}</td>
        </tr>
      </tbody>
    </table>
    <div
      class="pagination w3-bar w3-padding-16 w3-small"
      v-if="paging.total_list_cnt > 0"
    >
      <span class="pg">
        <a
          href="javascript:;"
          @click="fnPage(1)"
          class="first w3-button w3-border"
          >&lt;&lt;</a
        >
        <a
          href="javascript:;"
          v-if="paging.start_page > 10"
          @click="fnPage(`${paging.start_page - 1}`)"
          class="prev w3-button w3-border"
          >&lt;</a
        >
        <template v-for="(n, index) in paginavigation()">
          <template v-if="paging.page == n">
            <strong class="w3-button w3-border w3-green" :key="index">{{
              n
            }}</strong>
          </template>
          <template v-else>
            <a
              class="w3-button w3-border"
              href="javascript:;"
              @click="fnPage(`${n}`)"
              :key="index"
              >{{ n }}</a
            >
          </template>
        </template>
        <a
          href="javascript:;"
          v-if="paging.total_page_cnt > paging.end_page"
          @click="fnPage(`${paging.end_page + 1}`)"
          class="next w3-button w3-border"
          >&gt;</a
        >
        <a
          href="javascript:;"
          @click="fnPage(`${paging.total_page_cnt}`)"
          class="last w3-button w3-border"
          >&gt;&gt;</a
        >
      </span>
    </div>

    <!-- <div>
      <select class="w3-select" v-model="search_key">
        <option v-for="(value, key) in selectList" :key="key" :value="value">
          {{ key }}
        </option> -->
    <!-- <option value="">-선택-</option>
                <option value="author">작성자</option>
                <option value="title">제목</option>
                <option value="contents">내용</option> -->
    <!-- </select>
      &nbsp;
      <form class="w3-container">
        <input
          class="w3-input"
          type="text"
          v-model="search_value"
          @keyup.enter="fnPage()"
        />
      </form>
      &nbsp;
      <button
        class="w3-button w3-round w3-pale-green w3-hover-green"
        @click="fnPage()"
      >
        검색
      </button>
    </div> -->
  </div>
</template>

<script>
export default {
  data() {
    return {
      requestBody: {}, //리스트 페이지 데이터전송
      list: {}, //리스트데이터
      no: "", //게시판 숫자처리
      paging: {
        block: 0,
        end_page: 0,
        next_block: 0,
        page: 0,
        page_size: 0,
        prev_block: 0,
        start_index: 0,
        start_page: 0,
        total_block_cnt: 0,
        total_list_cnt: 0,
        total_page_cnt: 0,
      }, //페이징 데이터

      selectList: {
        "-선택-": "",
        번호: "emp_no",
        성별: "gender",
        생일: "birth_date",
        입사일: "hire_date",
      },
      // 검색조건을 서버단 dto나 entity에 세팅하면 기본값 설정이 필요없음
      //   empNo: 0,
      //   gender: "M",
      //   hireDateFrom: "0000-00-00",
      //   hireDateTo: "0000-00-00",
      //   birthDateFrom: "0000-00-00",
      //   birthDateTo: "0000-00-00",
      //   firstName: "aaaa",
      //   lastName: "bbbb",
      empNo: "",
      gender: "",
      hireDateFrom: "",
      hireDateTo: "",
      birthDateFrom: "",
      birthDateTo: "",
      firstName: "",
      lastName: "",

      page: this.$route.query.page ? this.$route.query.page : 1,
      size: this.$route.query.size ? this.$route.query.page : 10,
      search_key: this.$route.query.sk ? this.$route.query.sk : "",
      search_value: this.$route.query.sv ? this.$route.query.sv : "",
      keyword: this.$route.query.keyword,
      paginavigation: function () {
        //페이징 처리 for문 커스텀
        let pageNumber = [];
        let start_page = this.paging.start_page;
        let end_page = this.paging.end_page;
        for (let i = start_page; i <= end_page; i++) pageNumber.push(i);
        return pageNumber;
      },
    };
  },
  mounted() {
    this.fnGetList();
  },
  methods: {
    fnGetList() {
      // this.list = [
      //     {
      //         "idx": 1,
      //         "title": "제목1",
      //         "author": "작성자1",
      //         "created_at": "작성일시1"
      //     },
      //     {
      //         "idx": 2,
      //         "title": "제목2",
      //         "author": "작성자2",
      //         "created_at": "작성일시2"
      //     },
      //     {
      //         "idx": 3,
      //         "title": "제목3",
      //         "author": "작성자3",
      //         "created_at": "작성일시3"
      //     },
      //     {
      //         "idx": 4,
      //         "title": "제목4",
      //         "author": "작성자4",
      //         "created_at": "작성일시4"
      //     },
      // ]
      //   if (this.gender !== "") {
      //     this.search_key = "gender";
      //     this.search_value = this.gender;
      //   } else if (this.hireDateFrom !== "") {
      //     this.search_key = "hire_date";
      //     this.search_value = this.hire_date;
      //   } else if (this.emp_no !== "") {
      //     this.search_key = "emp_no";
      //     this.search_value = this.emp_no;
      //   }
      this.requestBody = {
        //데이터 전송
        // keyword: this.keyword,
        sk: this.search_key,
        sv: this.search_value,
        page: this.page,
        size: this.size,
        // ----------------나중에 추가---------------
        empNo: this.empNo,
        gender: this.gender,
        hireDateFrom: this.hireDateFrom,
        hireDateTo: this.hireDateTo,
        birthDateFrom: this.birthDateFrom,
        birthDateTo: this.birthDateTo,
        firstName: this.firstName,
        lastName: this.lastName,
        // ----------------나중에 추가---------------
      };

      this.$axios
        .get(this.$serverUrl + "/employee/list", {
          // this.$axios.get(this.$serverUrl + "/board/list", {
          params: this.requestBody,
          headers: {},
        })
        .then((res) => {
          // this.list = res.data //서버에서 데이터를 목록으로 보내므로 바로 할당하여 사용할 수 있다
          if (res.data.result_code === "OK") {
            this.list = res.data.data;
            this.paging = res.data.pagination;
            this.no =
              this.paging.total_list_cnt -
              (this.paging.page - 1) * this.paging.page_size;
          }
        })
        .catch((err) => {
          if (err.message.indexOf("Network Error") > -1) {
            alert("네트워크가 원할하지 않습니다./n잠시 후 다시 시도해주세요");
          }
        });
    },
    fnView(emp_no) {
      this.requestBody.emp_no = emp_no;
      this.$router.push({
        path: "./detail",
        query: this.requestBody,
      });
    },
    fnWrite() {
      this.$router.push({
        path: "./write",
      });
    },
    fnPage(n) {
      if (this.page !== n || n !== undefined) {
        this.page = n;
      }
      if (n === undefined) {
        this.page = 1;
      }

      this.fnGetList();
    },
  },
};
</script>
