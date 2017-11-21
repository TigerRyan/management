<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item><i class="el-icon-setting"></i>设置 </el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="handle-box">
            <el-button type="primary" icon="delete" class="handle-del mr10" @click="delAll">批量删除</el-button>
            <el-select v-model="select_cate" placeholder="筛选省份" class="handle-select mr10">
                <el-option key="1" label="北京市" value="北京市"></el-option>
                <el-option key="2" label="陕西省" value="陕西省"></el-option>
                <el-option key="3" label="昆明市" value="昆明市"></el-option>
                <el-option key="4" label="陕西省" value="陕西省"></el-option>
            </el-select>
            <el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>
            <el-button type="primary" icon="search" @click="search">搜索</el-button>
        </div>

        <el-table :data="data" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="id" label="序号" sortable width="90">
            </el-table-column>
            <el-table-column prop="name" label="名称" sortable width="150">
            </el-table-column>
            <el-table-column prop="class" label="类型" sortable width="90">
            </el-table-column>
            <el-table-column prop="location" label="位置"  width="100">
            </el-table-column>
            <el-table-column prop="address" label="地址" >
        </el-table-column>
            <el-table-column prop="tel" label="电话" width="150">
            </el-table-column>
            <el-table-column prop="website" label="网址" width="150">
            </el-table-column>

           
            <el-table-column label="操作" width="180">
                <template scope="scope">
                    <el-button size="small"
                               @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                    <el-button size="small" type="danger"
                               @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <div class="pagination">
            <el-pagination
                @current-change ="handleCurrentChange"
                layout="prev, pager, next"
                :total="1000">
            </el-pagination>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                url: './static/newlist.json', //请求的url
                tableData: [],                  //表格当前页数据
                cur_page: 1,                    //当前页码
                multipleSelection: [],          //多选数组
                select_cate: '',
                select_word: '',
                del_list: [],                   //删除列表
                is_search: false
            }
        },
        created(){
            this.getData();
        },
        computed: {
            data(){
                const self = this;
                return self.tableData.filter(function(d){    //关键字搜索
                    let is_del = false;
                    for (let i = 0; i < self.del_list.length; i++) {
                        if(d.name === self.del_list[i].name){
                            is_del = true;
                            break;
                        }
                    }
                    if(!is_del){                   
                        if(d.address.indexOf(self.select_cate) > -1 &&
                            (d.name.indexOf(self.select_word) > -1 ||
                            d.address.indexOf(self.select_word) > -1)
                        ){
                            return d;
                        }
                    }
                })
            }
        },
        methods: {
            handleCurrentChange(val){     //页码变更
                this.cur_page = val;
                this.getData();
            },
            getData(){
                this.$axios.get(this.url, {page:this.cur_page}).then((res) => {
                    this.tableData = res.data.list;
                console.log(res.data.list)
                console.log(this.tableData)
            })
            },

            search(){                               //进行搜索
                this.is_search = true;
            },
            formatter(row, column) {
                return row.address;
            },
            filterTag(value, row) {
                return row.tag === value;
            },
            handleEdit(index, row) {                  //进行编辑
                this.$message('编辑第'+(index+1)+'行');

            },
            handleDelete(index, row) {                 //进行单行删除
                 this.$message.error('删除第'+(index+1)+'行');
                // this.$confirm('此操作将永久删除用户 ' + this.name + ', 是否继续?', '提示', { type: 'warning' }) 
                //     .then(() => { // 向请求服务端删除 
                //     var resource = this.$resource(this.url + "{/id}"); 
                //     resource.delete({ id: this.id }) 
                //     .then((response) => { 
                //     this.$message.success('成功删除了用户' + this.name + '!');
                //      this.getData(); 
                //      }) 
                //     .catch((response) => { 
                //     this.$message.error('删除失败!'); 
                //     }); 
                //     }) .catch(() => { 
                //     this.$message.info('已取消操作!');
                //     }); 
            },
            delAll(){
                const self = this,
                    length = self.multipleSelection.length;
                let str = '';
                self.del_list = self.del_list.concat(self.multipleSelection);
                for (let i = 0; i < length; i++) {
                    str += self.multipleSelection[i].name + ' ';
                }
                self.$message.error('删除了'+str);
                self.multipleSelection = [];
            },                         //删除勾选项
            handleSelectionChange(val) {
                this.multipleSelection = val;
            }
        }
    }
</script>

<style scoped>
    .handle-box{
        margin-bottom: 20px;
    }
    .handle-select{
        width: 120px;
    }
    .handle-input{
        width: 300px;
        display: inline-block;
    }
</style>
