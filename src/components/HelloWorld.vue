<template>
 <div v-if="JSON.stringify(singleLaw) !== '{}'">
    <div class="law-box">
      <div class="law-con">
        <header class="law-title">
            <h1 class="law-title-text">{{singleLaw.title}}</h1>
            <div class="law-title-tips">
              <span class="law-title-tip">{{singleLaw.availability}}</span>
              <span class="law-title-tip">{{getTime(singleLaw.releaseDate)}}颁布</span>
              <span class="law-title-tip">{{getTime(singleLaw.effectiveDate)}}实施</span>
              <span class="law-title-tip">{{singleLaw.documentSymbol}}</span>
            </div>
        </header>
        <main class="law-main">
          <div class="law-main-header">{{singleLaw.comment}}</div>
          
            <h2 class="law-main-title" v-if="singleLaw.noticeTitle">{{singleLaw.noticeTitle}}</h2>
            <div class="law-main-header-symbol" v-if="singleLaw.noticeDocumentSymbol">{{singleLaw.noticeDocumentSymbol}}</div>
            <div class="law-main-brief" v-if="singleLaw.noticeContent" v-html="singleLaw.noticeContent"></div>
            <div v-if="singleLaw.noticeInscriber" class="law-main-discriber">
              <span  v-html="singleLaw.noticeInscriber.split(' ')[0]"></span>
              <span v-html="singleLaw.noticeInscriber.split(' ')[1]"></span>
            </div>
          
          <template v-if="singleLaw.reviseContent">
            <h2 class="law-main-title">{{singleLaw.reviseTitle}}</h2>
            <div class="law-main-brief" v-html="singleLaw.reviseContent"></div>
          </template>

          <div class="divider" v-if="singleLaw.noticeInscriber || singleLaw.reviseContent"></div>
          <div class="law-main-details">
            <div class="law-detail-first" v-if="singleLaw.lawItemNode"  :key="index" v-for="(itemSecend, index) in singleLaw.lawItemNode.children">
              <h3 :title="`${itemSecend.corder} ${itemSecend.title}`" v-if="itemSecend.name !== '条'" class="law-main-detail-h3" :id="'law'+itemSecend.id">{{itemSecend.corder}} {{itemSecend.title}}</h3>
              <div class="law-detail-secend">
                <!-- <div v-if="itemSecend.children" class="law-detail-secend-section" :id="'law'+itemSecend.id">{{itemSecend.corder}}</div> -->
                <div :data-id="itemthird.id" v-for="itemthird in itemSecend.children?itemSecend.children:[itemSecend]" :key="itemthird.id">
                    <h2 :title="`${itemthird.corder} ${itemthird.title}`" v-if="itemthird.children" :id="'law'+itemthird.id" class="law-main-detail-h2">{{itemthird.corder}} {{itemthird.title}}</h2>

                    <div :class="{'activeConArr': OpenIdObj.id === itemFour.id}" v-for="itemFour in itemthird.children?itemthird.children:[itemthird]" :key="itemFour.id">
                      <div class="law-detail-secend-title">
                        <div class="law-detail-secend-title-first">
                          <h4 v-if="itemFour.corder || itemFour.title" :title="itemFour.corder + ' ' + itemFour.title" :id="'law'+itemFour.id" class="law-main-detail-h4">{{itemFour.corder}} {{itemFour.title}}</h4> 
                        </div>
                      </div>
                      <div v-html="itemFour.content" class="law-detail-secend-main"></div>
                    </div>
                </div>
              </div>
            </div>
          </div>
        </main>
        <footer></footer>
      </div>
    </div>
    <div>
      <div>
        <div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import note from './dragCon'
import axios from "axios"
export default {
  name: 'law-library',
  components: {
    note
  },
  props: {
    showRight: {
      type: Boolean,
      default: true
    }
  },
  watch: {
    docId(val) {
      debugger
      this.showDetail(val)
    }
  },
  data() {
    return {
      docId: null,
      singleLaw: {},
      allActiveId: '',
      editorName: '',
      editorContent: '',
      editorShow: false,
      editorActiveid: '',
      OpenIdObj: {},
      navList: [],
      isCollect: JSON.stringify(this.singleLaw) && this.singleLaw.collectionFlag ? this.singleLaw.collectionFlag : 0
    }
  },
  methods: {
    clear(params) {
      var items = JSON.parse(JSON.stringify(params))
      for (const key in items) {
        if (items[key] === '') {
          delete items[key]
        } else if (Object.prototype.toString.call(items[key]) === '[object Array]' && items[key].length === 0) {
          delete items[key]
        } else if (JSON.stringify(items[key]) === '') {
          delete items[key]
        }
      }
      return items
    },
    getTime(time, hms) {
      var innerTime
      if (typeof time === 'string') {
        innerTime = Number(time)
      } else {
        innerTime = time
      }


      var date = new Date(time);
      if (!hms) {
        return date.getFullYear() + '-' + (date.getMonth() + 1) + '-' + date.getDate();
      } else {
        return date.getFullYear() + '-' + (date.getMonth() + 1) + '-' + date.getDate();
      }
    },
    showDetail(docId) {
      debugger
      axios({
        method: 'get',
        url: 'http://api.ibtool.cn/wiki/law/selectLawDetailPhone', // TODO 上线前需修改接口前缀为api.ibtool.cn(正式线)或119.23.31.217:9004(预发布)
        params: { docId: docId }
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
            this.singleLaw = res.data.msg
          }
      }).catch((error) => {
        this.$message.warning(error)
      })
    }
  },
  mounted() {
    debugger
    console.log('das')
    if (this.$route.query.id) {
      this.docId = Number(this.$route.query.id)
    }
  }
}
</script>
<style lang="scss">
.law-return {
  padding-left: 2px;
  padding-top: 10px;
  .law-return-btn {
    color: #2876b1;
    font-size: 12px;
    cursor: pointer;
  }
}
.law-box {
  display: flex;
  .law-menu {
    .law-collect {
      width: 280px;
      height: 40px;
      line-height: 40px;
      position: relative;
      margin-bottom: 20px;
      .law-collect-text {
        border: solid 1px #e2e2e2;
        position: fixed;
        width: 280px;
        background: #f7f7f7;
        font-size: 12px;
        color: #333333;
        display: flex;
        align-items: center;
        justify-content: center;
        cursor: pointer;
        i {
          font-size: 16px;
        }
        .colected {
          color: #e13b4b;
        }
        span {
          margin-right: 5px;
        }
        &:hover {
          background: rgba(247, 247, 247, 0.8);;
          .collect-true {
            display: none;
          }
          .collect-false{
            display: inline-block;
          }
        }
        .collect-false {
          display: none;
        }
      }
    }
    .law-tip {
      position: fixed;
      width: 280px;
      border: 1px solid #e2e2e2;
      .law-tip-title {
        height: 36px;
        background-color: #f7f7f7;
        font-size: 14px;
        color: #19233e;
        line-height: 36px;
        padding-left: 10px;
      }
      .law-tip-main {
        padding: 10px;
        .law-tip-item {
          display: flex;
          align-items: center;
          padding: 3px 0;
          .law-tip-item-span {
            color: #333333;
            width: 70px;
          }
        }
      }
    }
    .law-tree {
      margin-top: 200px;
      position: fixed;
      height: 100%;
      width: 100%;
    }
    .navigation {
      // bottom: 30px;
      width: 280px;
    }
  }
  .law-con {
    position: relative;
    padding-right: 10px;
    width: 100%;
    .law-title {
      padding-top: 20px;
      padding-bottom: 20px;
      border-bottom: 1px solid #e8e8e8;
      .law-title-text {
        font-size: 24px;
        color: #212527;
      }
      .law-title-tips {
        font-size: 12px;
        padding-top: 10px;
        color: #999999; 
        .law-title-tip {
					display: inline-block;
					position: relative;
					&+.law-title-tip {
						margin-left: 6px;
					}
					&::after {
            content: '';
            border: 1px solid rgba(102, 102, 102, .8);
            position: absolute;
            height: 12px;
            top: 2px;
            right: -6px;
					}
					&:last-child::after {
						display: none;
					}
				}
        .law-title-tip:last-child {
          &::after { 
            width: 0px;
          }
        }
      }
    }
    .law-main {
      padding: 10px 0;
      .law-main-header {
        font-size: 14px;
        color: #333333;
        line-height: 24px;
      }
      .law-main-header-symbol {
        padding: 3px 0;
        padding-bottom: 15px;
        text-align: center;
      }
      .law-main-title {
        padding-top: 30px;
        text-align: center;
        font-size: 18px;
        color: #333333;
      }
      .law-main-brief {
        font-size: 14px;
        color: #333333;
        line-height: 24px;
        text-indent: 2em;
        padding-bottom: 15px;
      }
      .divider {
        border-bottom: 1px solid #e2e2e2;
        width: 100%；
      }
      .law-main-discriber {
        text-align: right;
        span {
          display: block;
        }
      }
      .law-main-details {
        text-align: left;
        padding-top: 30px;
        padding-right: 30px;
        .law-detail-first {
          padding: 10px 0;
          .law-main-detail-h3 {
            font-size: 18px;
            color: #333333;
            line-height: 30px;
          }
          .law-main-detail-h2 {
            position: relative;
            left: -14px;
            height: 24px;
            border-radius: 1px;
            line-height: 24px;
            display: flex;
            align-items: center;
            background-color: #f7f7f7;
            margin: 10px 0;
            &::before {
              content: "";
              position: relative;
              left: 0px;
              height: 100%;
              width: 6px;
              background: #19223e;
              display: inline-block;
              margin-right: 10px;
              border-radius: 1px;
            }
          }
          .law-detail-secend {
            padding-left: 15px;
            padding-top: 5px;
            background-color: #fff;
            .activeConArr {
              background: #f7f7f7;
            }
            .ddddd {
              padding: 3px 5px;
              border-radius: 3px;
              margin-bottom: 10px;
              text-align: left;
              &:hover {
                background: #f7f7f7;
                border-radius: 3px;
                .law-main-detail-note-editor {
                  visibility: visible;
                }
              }
            }
            .law-detail-secend-section {
              font-size: 16px;
              line-height: 30px;
              height: auto;
              font-weight: bold;
              padding-right: 10px;
            }
            .law-detail-secend-title {
              // position: relative;
              display: flex;
              align-items: center;
              .law-detail-secend-title-first {
                width: 300px;
                // height: 30px;
                display: flex;
                align-items: center;
              }
              .law-detail-secend-title-secend {
                width: calc( 100% - 300px );
                text-align: right;
              }
              .law-main-detail-h4 {
                font-size: 18px;
                color: #333333;
                // line-height: 30px;
                margin: 0;
                display: inline-block;
              }
              .law-main-detail-tag {
                cursor: pointer;
                font-size: 12px;
                color: #333333;
                padding: 4px 10px;
                background-color: #e2e2e2;
                border-radius: 12px;
                margin-left: 5px;
              }
              .law-main-detail-note-editor {
                visibility: hidden;
                line-height: 24px;
                padding: 4px 10px;
                border-radius: 7px;
                margin-right: 10px;
                background-color: #19233e;
                color: #fff;
                cursor: pointer;
              }
            }
            .law-detail-secend-main {
              padding: 10px 0;
              line-height: 20px;
              color: #333333;
              font-size: 12px;
              p {
                margin: 0;
                text-indent: 2em;
              }
            }
            .law-detail-secend-interact {
              &.ibt-el-tabs {
                border: 1px solid #e2e2e2;
              }
              margin: 10px 15px;
            }
          }
        }
        .law-compare {
          font-size: 14px;
          padding: 5px 10px;
          .law-compare-title {
            color: #666666;
            padding-bottom: 5px;
          }
          .law-compare-con {
            // padding: 5px 0;
            .law-compare-con-title {
              line-height: 24px;
              color: #333333;
              padding-bottom: 5px;
              font-weight: bold;
            }
            .law-compare-con-item {
              line-height: 24px;
              color: #333333;
              letter-spacing: 1px;
            }
            .law-compare-con-divider {
              padding: 10px 0;
              border-bottom: 1px solid #ededed;
              &:last-child {
                border: none;
                padding-bottom: 0px;
              }
            }
          }
        }
        .law-diary {
          font-size: 14px;
          padding: 15px 20px;
          .law-diary-item {
            padding-top: 10px;
            border-bottom: 1px solid #ededed;
            padding-bottom: 10px;
            &:last-child {
              padding-bottom: 0px;
            }
            &:first-child {
              padding-top: 0px;
            }
            .law-diary-time {
              color: #666666;
              line-height: 24px;
              padding-bottom: 10px;
            }
            .law-diary-con {
              color: #333333;
              padding-bottom: 10px;
            }
            &:last-child {
              border: none;
            }
          }
        }
        .el-tab-pane {
          padding: 15px 20px;
          max-height: 200px;
          overflow-y: auto;
          overflow-x: hidden;
        }
        .nodata__con {
          position: relative;
          min-height: 50px;
          .nodata {
            position: absolute;
            top: 50%;
            transform: translate(-50%, -50%);
            left: 50%;
          }
        }
      }
    }
  }
}
.editor-box {
  position: fixed;
  width: 300px;
  height: 220px;
  background-color: #fff6d6;
  padding: 10px;
  z-index: 1002;
  cursor: all-scroll;
  .note-editor-box-title {
    color: #333333;
    font-size: 14px;
    padding: 15px 0px;
    text-align: center;
  }
  .note-editor-box-title {
    padding-bottom: 10px;

  }
  .note-editor-box-btn {
    display: flex;
    padding-top: 5px;
    align-items: center;
    justify-content:flex-end;
    padding-bottom: 10px;
    .el-button {
      &.el-button--medium {
        padding: 5px 11px; 
      }
    }
  }
  .note-editor-box-text {
    color: #333333;
    font-weight: bold;
    padding-bottom: 5px;
  }
}
</style>
