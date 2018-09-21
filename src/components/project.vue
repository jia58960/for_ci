<template>
  <div v-if="JSON.stringify(currentBonds) !== '{}'" class="bond-details-con dsadasdsadas mt-30 mlr-10" >
    <div class="insight-section " >
      <div class="text-default " >
        <span v-html="currentBonds.bondName"></span>
      </div>        
      <div class="insight-table mt-14">
        <div id="bond-title_1" class="insight-table__caption">项目基本信息</div>
        <div class="insight-tabs insight--dl bond-insight-info">
          <el-form class="condensed">
            <el-form-item label="债券名称" class="pl-16">
              <span v-html="currentBonds.bondName"></span>
            </el-form-item>
            <el-form-item label="债券类别：" class="pl-16">
              {{BONDTYPE_FOR_SHOW[currentBonds.bondType]}}
            </el-form-item>
            <el-form-item label="拟发行金额(亿元)：" class="pl-16">
              {{currentBonds.price}}
            </el-form-item>
            <el-form-item  label="发行人：" class="pl-16">
              <span class="info-color" v-html="currentBonds.publisher || '--'"></span>
            </el-form-item>
            <el-form-item label="承销商/管理人：" class="pl-16">
              <span class="info-color" v-html="currentBonds.manager"></span>
            </el-form-item>
            <el-form-item label="项目状态：" class="pl-16">
              {{BONDSTATUS_FOR_SHOW[currentBonds.status]}}
            </el-form-item>
            <el-form-item label="更新日期：" class="pl-16">
              {{currentBonds.updateTime}}
            </el-form-item>
          </el-form>
        </div>
      </div>
      <div id="bond-title_2" v-if="currentBonds.file" class="insight-table mt-40">
        <div class="insight-table__caption">信息披露文件及备查文件</div>
        <a id="preview" target="_blank" href="" style="display:none"></a>
        <p id="bond-title_2_M1"  v-if="currentBonds.file.M1" class="text-center">本次公司债券发行的募集文件</p>
        <el-table v-if="currentBonds.file.M1" :data="currentBonds.file.M1"   style="width: 100%" size="mini"  class="insight-tabs">
          <el-table-column show-header="false" type="index" label="序号" width="70"></el-table-column>
          <el-table-column show-header="false" label="文件名称">  
            <template slot-scope="scope">
              <a href="javascript:;" @click="handleYZPreview(scope.row.url)" class="href-item text-primary">{{ scope.row.name }}</a>
            </template>
          </el-table-column>
          <el-table-column show-header="false" prop="update" label="更新日期" width="100"></el-table-column>
        </el-table>

        <p id="bond-title_2_M2" v-if="currentBonds.file.M2" class="text-center">发行人关于本次公司债券发行的申请和授权文件</p>
        <el-table v-if="currentBonds.file.M2" :data="currentBonds.file.M2" style="width: 100%" size="mini" class="insight-tabs" >
          <el-table-column type="index" label="序号" width="70"></el-table-column>
          <el-table-column label="文件名称">
            <template slot-scope="scope">
              <a href="javascript:;" @click="handleYZPreview(scope.row.url)" class="href-item text-primary">{{ scope.row.name }}</a>
            </template>
          </el-table-column>
          <el-table-column prop="update" label="更新日期" width="100"></el-table-column>
        </el-table>


        <p id="bond-title_2_M3" v-if="currentBonds.file.M3" class="text-center">中介机构关于本次公司债券发行的文件</p>
        <el-table v-if="currentBonds.file.M3" :data="currentBonds.file.M3"   style="width: 100%" size="mini" class="insight-tabs">
          <el-table-column type="index" label="序号" width="70"></el-table-column>
          <el-table-column label="文件名称">
            <template slot-scope="scope">
              <a href="javascript:;" :id="`bond-title_3_${scope.$index}`"  @click="handleYZPreview(scope.row.url)" class="href-item text-primary">{{ scope.row.name }}</a>
            </template>
          </el-table-column>
          <el-table-column prop="update" label="更新日期" width="100"></el-table-column>
        </el-table>
        
        <p id="bond-title_2_M4" v-if="currentBonds.file.M4" class="text-center">财务及审计报告列表</p>
        <el-table v-if="currentBonds.file.M4" :data="currentBonds.file.M4"   style="width: 100%" size="mini" class="insight-tabs">
          <el-table-column type="index" label="序号" width="70"></el-table-column>
          <el-table-column label="文件名称">
            <template slot-scope="scope">
              <a href="javascript:;" @click="handleYZPreview(scope.row.url)" class="href-item text-primary">{{ scope.row.name }}</a>
            </template>
          </el-table-column>
          <el-table-column prop="update" label="更新日期" width="100"></el-table-column>
        </el-table>

        <p id="bond-title_2_M5" v-if="currentBonds.file.M5" class="text-center">担保文件列表</p>
        <el-table v-if="currentBonds.file.M5" :data="currentBonds.file.M5"   style="width: 100%" size="mini" class="insight-tabs">
          <el-table-column type="index" label="序号" width="70"></el-table-column>
          <el-table-column label="文件名称">
            <template slot-scope="scope">
              <a href="javascript:;" @click="handleYZPreview(scope.row.url)" class="href-item text-primary">{{ scope.row.name }}</a>
            </template>
          </el-table-column>
          <el-table-column prop="update" label="更新日期" width="100"></el-table-column>
        </el-table>

        <p id="bond-title_2_M6" v-if="currentBonds.file.M6" class="text-center">其他文件列表</p>
        <el-table v-if="currentBonds.file.M6" :data="currentBonds.file.M6"  style="width: 100%" size="mini" class="insight-tabs">
          <el-table-column type="index" label="序号" width="70"></el-table-column>
          <el-table-column label="文件名称">
            <template slot-scope="scope">
              <a href="javascript:;" @click="handleYZPreview(scope.row.url)" class="href-item text-primary">{{ scope.row.name }}</a>
            </template>
          </el-table-column>
          <el-table-column prop="update" label="更新日期" width="100" ></el-table-column>
        </el-table>
      </div>
      <div id="bond-title_3" v-if="currentBonds.feedback" class="insight-table mtb-40 ">
        <div class="insight-table__caption ">反馈意见及回复</div>
        <el-table :data="currentBonds.feedback" style="width: 100%" size="mini" class="insight-tabs">
          <el-table-column type="index" label="序号" width="70"></el-table-column>
          <el-table-column prop="name" label="反馈报告">
            <template slot-scope="scope">
              <a href="javascript:;" @click="handleYZPreview(scope.row.url)" class="href-item text-primary">{{ scope.row.name }}</a>
            </template>
          </el-table-column>
          <el-table-column prop="update" label="反馈日期" width="100"></el-table-column>
        </el-table>
      </div>
    </div>
  </div>
</template>
<script>
import querystring from 'querystring'
import axios from "axios"
export default {
  name: 'bond-project',
  data() {
    return {
      BONDTYPE_FOR_SHOW: ['小公募', '私募', 'ABS'],
      BONDSTATUS_FOR_SHOW: ['--', '已受理', '已反馈', '已接收反馈意见', '通过', '未通过', '--', '--', '终止', '中止', '已回复交易所意见'],
      bonds: [],
      currentBonds: {},
      docId: null
    }
  },
  watch: {
    docId(val) {
      this.showDetail(val)
    }
  },
  mounted() {
    if (this.$route.query.id) {
      this.docId = this.$route.query.id
    }
  },
  methods: {
    showDetail(docId) {
      axios({
        method: 'post',
        url: 'http://api.ibtool.cn/insight/bond/detail', // TODO 上线前需修改接口前缀为api.ibtool.cn(正式线)或119.23.31.217:9004(预发布)
        data: querystring.stringify({ id: docId })
      }).then((res) => {
          debugger
          if (!res || !res.data || res.data.code !== '0') {
            return
          }
          if (!res.data.msg) {
            this.$alert('该条无数据', '提示', {
                confirmButtonText: '确定'
            })
          } else {
            this.currentBonds = res.data.msg
          }
      }).catch((error) => {
        this.$message.warning(error)
      })
    },
    handleYZPreview(url) {
      var a = document.querySelector('#preview')
      a.setAttribute('href', url)
      a.click()
    }
  }
}
</script>

<style lang="scss" >

.bond-details-con {
  text-align: left;
  margin: 0 auto;
  font-size: 12px;
  background-color: #fff;
  .insight-section {
    // margin-left: 10px;
    width: 100%;
    .text-default  {
      font-size: 12px;
    }
  }

  .insight-table {
    margin-top: 20px;
    .insight-table__caption {
      margin-bottom: 20px;
      font-size: 16px;
    }
    .insight-tabs {
      .el-form-item  {
        display: flex;
        line-height: 24px;
        min-height: 24px;
        height: auto;
      }
    }
    .text-center {
      text-align: center;
    }
    .el-table {
      border: 1px solid #e2e2e2;
      .el-table__row {
        height: 36px;
        td {
          height: 36px;
          border-bottom: 1px solid #ebeef5;
          .cell {
              box-sizing: border-box;
              overflow: hidden;
              text-overflow: ellipsis;
              white-space: normal;
              word-break: break-all;
            .href-item {
              color: #2876b1;
            }
          }
        }
        &:hover {
            background-color: #f5f7fa;
        }
      }
      .el-table__body-wrapper {
        .el-table__empty-block {
          min-height: 36px;
          text-align: center;
          line-height: 36px;
        }
      }
    }
  }

  .insight-tabs {
     border-bottom: none;
    .insight-table__caption {
      margin-bottom: 20px;
      font-size: 16px;
    }
    .insight-tabs {
      font-size: 12px;
      &.el-table {
        &::before {
          height: 0px;
        }

        .el-table__body-wrapper {
          .href-item {
            color: #2876b1;
          }
        }
      }
    }
    .el-form {
      .el-form-item {
        margin-bottom: 5px;
        display: flex;
        .el-form-item__label {
          min-width: 60px;
          font-size: 12px;
          line-height: 24px;
        }
        .el-form-item__content {
          font-size: 12px;
          margin-left: 0px;
        }
      }
    }
    
    &.bond-insight-info.insight-tabs {
      border: none;
      .condensed.el-form {
        .el-form-item {
          border-bottom: 1px dashed #eeeeee;
          .el-form-item__content {
            line-height: 24px;
            cursor: default;
            .bond-info-special__text {
              color: #2876b1;
            }
          }
        }
      }
    }

    &.el-table {
      &:before {
        background-color: #e2e2e2;
      }
      .cell {
        padding-left: 10px;
        padding-right: 10px;
        line-height: 1.4;
      }

      th,
      td {
        &:first-child {
          .cell {
            padding-left: 15px;
          }
        }
      }

      th {
        background-color: #f7f7f7;
      }

      &.el-table--mini {
        th {
          height: 36px;
        }

        .th-row {
          color: #333;
          text-align: center;
        }
      }

      .el-table__header-wrapper thead div {
        color: #333;
      }
    }
  }
}
</style>
