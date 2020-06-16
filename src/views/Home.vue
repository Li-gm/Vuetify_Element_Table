<template>
  <v-row no-gutters style="width: 1628px;margin: 0 auto;">
    <v-col cols="12" sm="12" style="margin-top: 50px">
      <Table
        :data="tableData"
        highlight-current-row
        @sort-change="sortChange"
        border
        empty-text=" Warnings: No results were obtained under this model!"
        max-height="800"
      >
        <TableColumn
          type="index"
          width="50">
        </TableColumn>
        <TableColumn
          v-for="(header,index) in headers"
          :prop="header.key"
          :label="header.lable"
          :sortable="header.type==='sort'"
          :column-key='header.key'
          width="150"
        >
          <template slot="header" slot-scope="scope">
            {{header.lable}}
            <template v-if="header.type==='classify'">
              <v-menu offset-y  :close-on-content-click="false">
                <template v-slot:activator="{ on }">
                  <v-icon small v-on="on" v-on:click.stop="">{{!headersFilter[header.key].length? 'mdi-filter-outline':'mdi-filter-plus'}}</v-icon>
                </template>
                <v-card>
                  <v-list>
                    <template>
                      <v-list-item xs="12" class="filterItemContainer">
                        <v-list-item-content>
                          <div style="margin-left: 10px">
                            <v-checkbox v-for="checkInfo in header.select" v-model="headersFilter[header.key]" :label="checkInfo" :value="checkInfo"></v-checkbox>
                          </div>
                          <div style="text-align: center">
                            <v-btn color="#ff5f5f" style="margin-right: 10px" dark @click="filterEvent(header.key,headersFilter[header.key])">筛选</v-btn>
                            <v-btn color="#ff5f5f" dark @click="ResetEvent(header.key,'classify')">重置</v-btn>
                          </div>
                        </v-list-item-content>
                      </v-list-item>
                    </template>
                  </v-list>
                </v-card>
              </v-menu>
            </template>
            <template v-if="header.type==='filter'">
              <v-menu offset-y  :close-on-content-click="false">
                <template v-slot:activator="{ on }">
                  <v-icon small v-on="on" v-on:click.stop="">{{headersFilter[header.key] === '' && operatorFilter[header.key] === ''? 'mdi-filter-outline':'mdi-filter-plus'}}</v-icon>
                </template>
                <v-card>
                  <v-list>
                    <template>
                      <v-list-item xs="12" class="filterItemContainer">
                        <v-list-item-content>
                          <v-radio-group v-model="operatorFilter[header.key]" row style="margin-left: 10px">
                            <v-radio
                              v-for="radioInfo in operatorList"
                              :key="radioInfo.value"
                              :label="radioInfo.text"
                              :value="radioInfo.value"
                            ></v-radio>
                          </v-radio-group>
                          <v-text-field
                            v-model="headersFilter[header.key]"
                            label="数值"
                            type="number"
                          ></v-text-field>
                          <div style="text-align: center">
                            <v-btn color="#ff5f5f" style="margin-right: 10px" dark @click="filterEvent(header.key,headersFilter[header.key])">筛选</v-btn>
                            <v-btn color="#ff5f5f" dark @click="ResetEvent(header.key,'filter')">重置</v-btn>
                          </div>
                        </v-list-item-content>
                      </v-list-item>
                    </template>
                  </v-list>
                </v-card>
              </v-menu>
            </template>
            <template v-if="header.type==='match'">
              <v-menu offset-y  :close-on-content-click="false">
                <template v-slot:activator="{ on }">
                  <v-icon small v-on="on" v-on:click.stop="">{{headersFilter[header.key] === ''? 'mdi-filter-outline':'mdi-filter-plus'}}</v-icon>
                </template>
                <v-card>
                  <v-list>
                    <template>
                      <v-list-item xs="12" class="filterItemContainer">
                        <v-list-item-content>
                          <v-text-field
                            v-model="headersFilter[header.key]"
                            label="模糊匹配"
                          ></v-text-field>
                          <div style="text-align: center">
                            <v-btn color="#ff5f5f" style="margin-right: 10px" dark @click="filterEvent(header.key,headersFilter[header.key])">筛选</v-btn>
                            <v-btn color="#ff5f5f" dark @click="ResetEvent(header.key)">重置</v-btn>
                          </div>
                        </v-list-item-content>
                      </v-list-item>
                    </template>
                  </v-list>
                </v-card>
              </v-menu>
            </template>
          </template>
          <template slot-scope="scope">
            <div>{{scope.row[header.key]}}</div>
          </template>
        </TableColumn>
      </Table>
      <Pagination
        background
        layout="prev, pager, next"
        :total='pagination.total'
        :current-page="pagination.page"
        @current-change="currentPageChange"
        style="margin-top: 10px"
      >
      </Pagination>
    </v-col>
  </v-row>
</template>

<script>
  import {Table,TableColumn,Pagination} from 'element-ui'
  import 'element-ui/lib/theme-chalk/index.css';
  export default {
    name: 'Home',
    components: {Table,TableColumn,Pagination},
    data(){
      return{
        headers:[
          {
            key: "sampleID",
            lable: "SampleID",
            select: ["SRR1584524", "SRR1584527", "SRR1584528", "SRR1584529", "SRR1584530", "SRR1584525", "SRR1584526"],
            type: "classify",
          },
          {
            key: "gene",
            lable: "GeneSymbol",
            type: "match",
          },
          {
            key: "Chr",
            lable: "Chr",
            type: "sort",
          },
          {
            key: "PopFreqMax",
            lable: "PopFreqMax",
            type: "filter",
          }
        ],
        tableData:[
          {
            sampleID:'001',
            gene:'gene1',
            Chr:1,
            PopFreqMax:0.01,
          },
          {
            sampleID:'002',
            gene:'gene2',
            Chr:2,
            PopFreqMax:0.02,
          },
          {
            sampleID:'003',
            gene:'gene3',
            Chr:3,
            PopFreqMax:0.03,
          },
          {
            sampleID:'003',
            gene:'gene3',
            Chr:3,
            PopFreqMax:0.03,
          },
          {
            sampleID:'003',
            gene:'gene3',
            Chr:3,
            PopFreqMax:0.03,
          },
          {
            sampleID:'003',
            gene:'gene3',
            Chr:3,
            PopFreqMax:0.03,
          },
          {
            sampleID:'003',
            gene:'gene3',
            Chr:3,
            PopFreqMax:0.03,
          },
          {
            sampleID:'003',
            gene:'gene3',
            Chr:3,
            PopFreqMax:0.03,
          }
        ],
        pagination:{
          total:0,
          page:1,
        },
        sort:{
          prop:null,
          order:null
        },//排序
        operatorList:[
          {
            text:'>',
            value:'$gt',
          },
          {
            text:'>=',
            value:'$gte',
          },
          {
            text:'<',
            value:'$lt',
          },
          {
            text:'=<',
            value:'$lte',
          },
          {
            text:'=',
            value:'$eq',
          }
        ],
        headersFilter:{},
        operatorFilter:{},//符号的选项
        submitFilter:{},//提交时筛选条件
      }
    },
    created(){
      this.initData();
    },
    methods:{
      initData(){
        for(let i = 0;i<this.headers.length;i++){
          if(this.headers[i].type === 'filter'){
            this.$set(this.headersFilter, this.headers[i].key, '')
            this.$set(this.operatorFilter, this.headers[i].key, '')
          }else if(this.headers[i].type === 'classify'){
            this.$set(this.headersFilter, this.headers[i].key, [])
          }else if(this.headers[i].type === 'match'){
            this.$set(this.headersFilter, this.headers[i].key, '')
          }
        }
      },
      sortChange(sort){
        this.sort.prop = sort.prop;
        this.sort.order = sort.order
      },
      currentPageChange(page){
        this.pagination.page = page;
      },
      filterEvent(key,val){
        if(this.operatorFilter[key] === ''){
          alert('请选运算符！');
          return;
        }
        if(val === ''){
          alert('请输入内容！');
          return;
        }
        if (!val.length) {
          alert('请勾选内容！');
          return;
        }
        if(this.operatorFilter[key]!== undefined && this.operatorFilter[key]!== ''){
          this.submitFilter[key] = {
            type:this.operatorFilter[key],
            value:val,
          };
        }else {
          this.submitFilter[key] = val;
        }
      },
      ResetEvent(key,val){
        this.$set(this.headersFilter, key, '');
        if(this.operatorFilter[key] !== undefined){
          this.$set(this.operatorFilter, key, '')
        }
        delete this.submitFilter[key];
      }
    }
  }
</script>
