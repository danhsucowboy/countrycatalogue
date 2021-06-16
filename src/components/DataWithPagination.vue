<template>
  <div>
      <div>
          Search: <input text="type" v-model.lazy="query"/>
      </div>
   
    <!--分頁部分-->
    <div class="row justify-content-center">
      <nav aria-label="Page navigation example">
        <ul class="pagination">
          <li
            class="page-item"
            :class="['page-item',{disabled:currentPage===1}]"
            @click.prevent="setPage(currentPage-1)"
          >
            <a class="page-link" href="#">Prev</a>
          </li>
          <li
            v-for="page in totalPage"
            :key="page"
            class="page-item"
            :class="['page-item',{active:currentPage === page}]"
            @click.prevent="setPage(page)"
          >
            <a class="page-link" href="#">{{page}}</a>
          </li>
          <li
            class="page-item"
            :class="['page-item',{disabled:currentPage===totalPage}]"
            @click.prevent="setPage(currentPage+1)"
          >
            <a class="page-link" href="#">Next</a>
          </li>
        </ul>
      </nav>
    </div>
   <!--表格部分-->
   <table style="margin:0 auto;">
      <thead>
        <tr>
            <th scope="col">國旗</th>
            <th scope="col" @click="sorting('name')">國家名稱</th>
            <th scope="col">2位國家代碼</th>
            <th scope="col">3位國家代碼</th>
            <th scope="col">母語名稱</th>
            <th scope="col">替代國家名稱</th>
            <th scope="col">國際電話區號</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item,index) in filterRows.slice(pageStart, pageStart + offset)" :key="index">
          <td>
              <img v-bind:src="item.flag" style="width: 10vw;">
          </td>
          <td>{{item.name}}</td>
          <td>{{item.alpha2Code}}</td>
          <td>{{item.alpha3Code}}</td>
          <td>{{item.nativeName}}</td>
          <td>{{item.altSpellings}}</td>
          <td>{{item.callingCodes}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "DataWithPagination",
  props: {
    // countries: {
    //   type: Array,
    //   required: true
    // }
  },
  data() {
    return {
      tableData: [],
      postObj: {
        name: '',
        alpha2Code: '',
        alpha3Code: '',
        nativeName:'',
        altSpellings: '',
        callingCodes: '',
      },
      query: "",
      currentPage: 1,
      offset: 25,
      currnetSortDir: "asc",
      currentSortCol: "name",
    };
  },
  computed: {
    filterRows() {
      let query = this.query.toLowerCase();
      let originData = this.tableData;
      if (!this.query.match(/^[ ]*$/)) {
        return originData.filter(d => {
          return d.name.toLowerCase().indexOf(query) > -1;
        });
      } else {
        return originData;
      }
    },
    pageStart() {
      return (this.currentPage - 1) * this.offset;
    },
    totalPage() {
      return Math.ceil(this.filterRows.length / this.offset);
    }
  },
  methods: {
    readDataFromAPI() {
    //   this.loading = true;
    //   const { page, itemsPerPage } = this.options;
    //   let pageNumber = page - 1;
      axios
        .get(
          "https://restcountries.eu/rest/v2/all"
        )
        .then((response) => {
          //Then injecting the result to datatable parameters.
          this.tableData = [...response.data];
        //   console.log(response.data);
        });
    },
    setPage(idx) {
      if (idx <= 0 || idx > this.totalPage) {
        return;
      }
      this.currentPage = idx;
    },
    sorting(col) {
      this.currentPage = 1;

      this.currentSortDir = this.currentSortDir === "asc" ? "desc" : "asc";

      this.tableData.sort(this.sortBy(col, this.currentSortDir));
    },
    sortBy(property, order) {
      this.currnetSortDir=order;
      return function(a, b) {
        const varA = a[property].toLowerCase().trim();
        const varB = b[property].toLowerCase().trim();

        let comparison = 0;
        if (varA > varB) comparison = 1;
        else if (varA < varB) comparison = -1;
        return order === "desc" ? comparison * -1 : comparison;
      };
    }
  },
  mounted() {
    this.readDataFromAPI();
  },
  watch: {
    filterRows: {
      deep: true,
      handler: function() {
        this.currentPage = 1;
      }
    }
  }
};
</script>