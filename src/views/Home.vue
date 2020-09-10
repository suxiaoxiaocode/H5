<template>
  <div>
    <!-- 填写个人信息 -->
    <van-dialog v-model="isWelcome" class="content-box" :show-confirm-button="false">
      <div class="information" v-if="isWelcome">
        <div class="title">欢迎参加考试</div>
        <div class="sub-title">请填写您的信息</div>
        <div class="field-container">
          <div class="field-wrap">
            <!-- <img src="../../assets/images/car.jpg" class="dialog-name-img" /> -->
            <van-field
              v-model="name"
              placeholder="请输入姓名"
              :right-icon="nameFocus && name.length > 0 ? 'clear' : ''"
              @click-right-icon="clearName"
              @focus="nameFocus = true"
              @blur="nameBlur"
            />
          </div>
          <div class="field-wrap">
            <!-- <img src="../../assets/images/car.jpg" class="dialog-name-img" /> -->
            <van-field
              v-model="hospital"
              placeholder="请输入单位名称"
              :right-icon="hospitalFocus && hospital.length > 0 ? 'clear' : ''"
              @click-right-icon="clearHospital"
              @focus="hospitalFocus = true"
              @blur="hospitalBlur"
            />
          </div>
        </div>
        <van-button
          class="dialog-button"
          type="info"
          :class="validateUserInfo() ? '' : 'disabled'"
          @click="saveUserInfo()"
        >确定</van-button>
        <div class="dialog-tech" v-show="hidshow">由敬信科技提供技术支持</div>
      </div>
    </van-dialog>
    <!-- 答题 -->
    <div class="iviwe" v-if="!isWelcome">
      <div class="content">
        <div class="head-box">国家基本公共卫生服务项目</div>
        <div class="head">市级专家技能培训班考核</div>
        <!-- 单选题 -->
        <div class="option-title">一、单选题（每题4分，共15题）</div>
        <div class="option" v-for="(item,index) in examList.slice(0,15)" :key="index+'-examList'">
          <div class="problem">{{item.title}}</div>
          <div class="answer">
            <div class="option-answer">
              <van-radio-group
                :value="optionRuslt[item.id]"
                v-for="(ite,idx) in item.optionList "
                :key="idx+'-optionRuslt'"
              >
                <van-radio
                  @click="choose(item.id,idx)"
                  :checked-color="!isSubmit&&optionRuslt[item.id]!=item.options?'#F12433':'#01A551'"
                  class="answer-option"
                  :name="option[idx]"
                >{{ite}}</van-radio>
              </van-radio-group>
              <div
                v-if="!isSubmit&&optionRuslt[item.id]!=item.options"
                class="queType-title"
              >正确答案：{{item.options}}</div>
            </div>
          </div>
        </div>
        <!-- 判断题 -->
        <div class="option-title">二、判断题（每题4分，共10题）</div>
        <div class="option" v-for="(item,index) in examList.slice(15,25)" :key="index+'-title'">
          <div class="problem">{{item.title}}</div>
          <div class="answer">
            <div class="option-answer">
              <van-radio-group
                :value="optionRuslt[item.id]"
                v-for="(ite,idx) in item.optionList "
                :key="idx+'-uslt'"
              >
                <van-radio
                  @click="choose(item.id,idx)"
                  :checked-color="!isSubmit&&optionRuslt[item.id]!=item.options?'#F12433':'#01A551'"
                  class="answer-option"
                  :name="option[idx]"
                >{{ite}}</van-radio>
              </van-radio-group>
              <div
                v-if="!isSubmit&&optionRuslt[item.id]!=item.options"
                class="queType-title"
              >正确答案：{{item.options}}</div>
            </div>
          </div>
        </div>
        <div
          @click="submitExam"
          :class="[Object.keys(optionRuslt).length==examList.length&&isSubmit?'submitColor':'disSubmitColor']"
        >{{submitTitle}}</div>

        <div class="show-box" v-if="isDialog">
          <div class="submit-title">您的考试成绩</div>
          <div class="gradeNum">
            {{gradeNum}}
            <span>分</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      docmHeight: document.documentElement.clientHeight, //默认屏幕高度
      showHeight: document.documentElement.clientHeight, //实时屏幕高度
      hidshow: true, //显示或者隐藏footer,
      isDialog: false, //提交完弹窗显示分数
      examList: [
        {
          id: 0,
          optionList: ['A.2008', 'B.2009', 'C.2010', 'D.2011'],
          options: 'B',
          queType: 0,
          sort: 0,
          title: '1.国家基本公共卫生服务项目自____年启动以来，在基层医疗卫生机构普遍开展。'
        },

        {
          id: 17,
          optionList: ['A.对', 'B.错'],
          options: 'A',
          queType: 0,
          sort: 17,
          title: '3.基本公共卫生服务支出责任实行中央分档分担办法，广东省的中央分担比例为30%？'
        },
        {
          id: 1,
          optionList: ['A.29', 'B.30', 'C.31', 'D.32'],
          options: 'C',
          queType: 0,
          sort: 1,
          title: '2.目前，国家基本公共卫生服务项目有____项？'
        },
        {
          id: 18,
          optionList: ['A.对', 'B.错'],
          options: 'A',
          queType: 0,
          sort: 18,
          title: '4.居民健康档案管理属于12大类原基本公共卫生服务内容？'
        }
      ],
      // examList: [
      //   {
      //     id: 0,
      //     optionList: ['A.2008', 'B.2009', 'C.2010', 'D.2011'],
      //     options: 'B',
      //     queType: 0,
      //     sort: 0,
      //     title: '1.国家基本公共卫生服务项目自____年启动以来，在基层医疗卫生机构普遍开展。'
      //   },
      //   {
      //     id: 1,
      //     optionList: ['A.29', 'B.30', 'C.31', 'D.32'],
      //     options: 'C',
      //     queType: 0,
      //     sort: 1,
      //     title: '2.目前，国家基本公共卫生服务项目有____项？'
      //   },
      //   {
      //     id: 2,
      //     optionList: ['A.29', 'B.30', 'C.31', 'D.32'],
      //     options: 'A',
      //     queType: 0,
      //     sort: 2,
      //     title: '3.目前，广州市实施的国家基本公共卫生服务项目有____项？'
      //   },
      //   {
      //     id: 3,
      //     optionList: ['A.乡镇卫生院', 'B.村卫生室', 'C.社区卫生服务中心（站）', 'D.私人诊所'],
      //     options: 'D',
      //     queType: 0,
      //     sort: 3,
      //     title: '4.下面哪些场所不能提供12大类原基本公共卫生服务项目？'
      //   },
      //   {
      //     id: 4,
      //     optionList: ['A.自费', 'B.部分自费', 'C.免费'],
      //     options: 'C',
      //     queType: 0,
      //     sort: 4,
      //     title: '5.国家提供的基本公共卫生服务项目所涉及的服务内容，是____的。'
      //   },
      //   {
      //     id: 5,
      //     optionList: ['A.60元', 'B.65元', 'C.70元', 'D.75元'],
      //     options: 'C',
      //     queType: 0,
      //     sort: 5,
      //     title: '6.2020年广州市原12项国家基本公共卫生服务项目人均补助标准为？'
      //   },
      //   {
      //     id: 6,
      //     optionList: ['A.组织管理', 'B.人员配置', 'C.项目执行', 'D.项目效果'],
      //     options: 'B',
      //     queType: 0,
      //     sort: 6,
      //     title: '7.以下哪项不属于国家基本公共卫生服务项目绩效评估内容？'
      //   },
      //   {
      //     id: 7,
      //     optionList: ['A.logo', 'B.国家基本公共卫生服务项目', 'C.免费', 'D.专线名称'],
      //     options: 'D',
      //     queType: 0,
      //     sort: 7,
      //     title: '8.以下哪项不是国家基本公共卫生服务项目宣传需包含的内容？'
      //   },
      //   {
      //     id: 8,
      //     optionList: ['A.人员经费', 'B.公用经费', 'C.购置大型设备', 'D.绩效评价补助'],
      //     options: 'C',
      //     queType: 0,
      //     sort: 8,
      //     title: '9.原基本公共卫生服务项目转移支付资金的使用范围不含？'
      //   },
      //   {
      //     id: 9,
      //     optionList: ['A.2017', 'B.2018', 'C.2019', 'D.2020'],
      //     options: 'C',
      //     queType: 0,
      //     sort: 9,
      //     title:
      //       '10.由财政部、国家卫生健康委、国家医疗保障局和国家中医药管理局四部门联合印发的《基本公共卫生服务补助资金管理办法》，印发时间是？'
      //   },
      //   {
      //     id: 10,
      //     optionList: ['A.孕35周出生', 'B.出生体重4000克', 'C.血清胆红素15.1umol/L', 'D.先天性腭裂'],
      //     options: 'C',
      //     queType: 0,
      //     sort: 10,
      //     title: '11.以下哪种情况不属于高危新生儿？'
      //   },
      //   {
      //     id: 11,
      //     optionList: ['A.<20岁或≥35岁', 'B.<18岁或≥37岁', 'C.<18岁或≥35岁', 'D.<20岁或≥37岁'],
      //     options: 'C',
      //     queType: 0,
      //     sort: 11,
      //     title: '12.母亲初产年龄为____岁时，新生儿纳入高危儿管理？'
      //   },
      //   {
      //     id: 12,
      //     optionList: [
      //       'A.6月龄内：1次/1-2个月',
      //       'B.1岁以上：至少每6个月1次',
      //       'C.6月龄以上：1次/2-3个月',
      //       'D.1岁以上：至少每3个月1次'
      //     ],
      //     options: 'B',
      //     queType: 0,
      //     sort: 12,
      //     title: '13.高危儿随访频率以下哪项是错误的？'
      //   },
      //   {
      //     id: 13,
      //     optionList: [
      //       'A.早产儿：实际年龄满24月龄，体格生长及发育评价达同龄足月儿正常水平时；暂时不能结案者管理至30月龄',
      //       'B.营养性疾病：连续两次评估无异常',
      //       'C.心理行为发育偏离：连续两次评估正常并年满1周岁',
      //       'D.其他情况依据相关指南规范或实际转归，酌情确定结案时间'
      //     ],
      //     options: 'A',
      //     queType: 0,
      //     sort: 13,
      //     title: '14.关于专案管理的结案，以下哪项是错误的？'
      //   },
      //   {
      //     id: 14,
      //     optionList: [
      //       'A.高危儿身心疾病的诊断、干预、治疗、专案管理',
      //       'B.辖区各级医疗机构的技术指导与评价，相关信息管理工作',
      //       'C.负责指导和开展健康教育工作，制订作计划，编制健康教育材料',
      //       'D.以上都是'
      //     ],
      //     options: 'D',
      //     queType: 0,
      //     sort: 14,
      //     title: '15.以下地市级及以上妇幼保健机构的高危儿管理职责正确的是？'
      //   },
      //   {
      //     id: 15,
      //     optionList: ['A.对', 'B.错'],
      //     options: 'A',
      //     queType: 0,
      //     sort: 15,
      //     title: '1.目前，国家基本公共卫生服务项目包括原基本公共卫生服务内容和新划入的基本公共卫生服务内容？'
      //   },
      //   {
      //     id: 16,
      //     optionList: ['A.对', 'B.错'],
      //     options: 'A',
      //     queType: 0,
      //     sort: 16,
      //     title: '2.《基本公共卫生服务补助资金管理办法》规定的转移支付资金采用因素法分配？'
      //   },
      //   {
      //     id: 17,
      //     optionList: ['A.对', 'B.错'],
      //     options: 'A',
      //     queType: 0,
      //     sort: 17,
      //     title: '3.基本公共卫生服务支出责任实行中央分档分担办法，广东省的中央分担比例为30%？'
      //   },
      //   {
      //     id: 18,
      //     optionList: ['A.对', 'B.错'],
      //     options: 'A',
      //     queType: 0,
      //     sort: 18,
      //     title: '4.居民健康档案管理属于12大类原基本公共卫生服务内容？'
      //   },
      //   {
      //     id: 19,
      //     optionList: ['A.对', 'B.错'],
      //     options: 'B',
      //     queType: 0,
      //     sort: 19,
      //     title: '5.地方病防治属于12大类原基本公共卫生服务内容？'
      //   },
      //   {
      //     id: 20,
      //     optionList: ['A.对', 'B.错'],
      //     options: 'A',
      //     queType: 0,
      //     sort: 20,
      //     title: '6.国家基本公共卫生服务项目服务对象主要是辖区常住居民？'
      //   },
      //   {
      //     id: 21,
      //     optionList: ['A.对', 'B.错'],
      //     options: 'B',
      //     queType: 0,
      //     sort: 21,
      //     title: '7.广州市开展了贫困地区儿童营养改善项目管理工作？'
      //   },
      //   {
      //     id: 22,
      //     optionList: ['A.对', 'B.错'],
      //     options: 'A',
      //     queType: 0,
      //     sort: 22,
      //     title: '8.广州市没有开展贫困地区新生儿疾病筛查项目管理工作？'
      //   },
      //   {
      //     id: 23,
      //     optionList: ['A.对', 'B.错'],
      //     options: 'A',
      //     queType: 0,
      //     sort: 23,
      //     title:
      //       '9.高危儿童是指在胎儿到3岁内具有可能影响身心发育的各种高危因素（包括生物、心理、社会环境等因素）的儿童？'
      //   },
      //   {
      //     id: 24,
      //     optionList: ['A.对', 'B.错'],
      //     options: 'A',
      //     queType: 0,
      //     sort: 24,
      //     title:
      //       '10.母亲患有糖尿病、甲状腺功能异常、严重感染（如风疹病毒、巨细胞病毒等）、中度以上妊娠期高血压综合征等的新生儿列入高危儿管理？'
      //   }
      // ], //题目列表
      option: ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P'], //选项
      optionRuslt: [], //单选提交选择选项
      optionRusltTrue: [], //判断提交选择选项
      isWelcome: false, //显示填写资料
      name: '', //性别
      hospital: '', //单位
      nameFocus: false, //姓名失去焦点
      hospitalFocus: false, //单位失去焦点
      correct: [
        'B',
        'C',
        'A',
        'D',
        'C',
        'C',
        'B',
        'D',
        'C',
        'C',
        'C',
        'C',
        'B',
        'A',
        'D',
        'A',
        'A',
        'A',
        'A',
        'B',
        'A',
        'B',
        'A',
        'A',
        'A'
      ], //正确答案
      gradeNum: '', //分数
      correctNum: 0, //正确率
      submitTitle: '提交', //提交文案
      isSubmit: true //是否第一次提交
    }
  },
  created() {},
  mounted() {
    //兼容安卓输入框
    var that = this
    window.onresize = () => {
      return (() => {
        window.fullHeight = document.documentElement.clientHeight
        that.showHeight = window.fullHeight
      })()
    }
  },
  methods: {
    //提交考试结果
    submitExam() {
      if (Object.keys(this.optionRuslt).length == this.examList.length && this.isSubmit) {
        for (var i = 0; i < this.correct.length; i++) {
          if (this.optionRuslt[i] == this.correct[i]) {
            this.correctNum++
          }
        }
        this.gradeNum = parseInt((this.correctNum / 25) * 100)
        const data = {
          answers: [],
          name: this.name,
          title: this.hospital,
          grade: this.gradeNum
        }
        //单选题
        this.optionRuslt.forEach((element, index) => {
          data.answers.push({
            answer: this.optionRuslt[index],
            id: this.examList[index].id
          })
        })
        this.isSubmit = false //不可重复提交
        // this.$http.h5.submitExamList(data).then(res => {
        //   if (res.code == '0') {
        //     this.submitTitle = '提交成功'

        //     this.isDialog = true //底部分数
        //     this.bakToBot() //回到底部
        //   } else {
        //     Toast('请求出错')
        //   }
        // })
      } else {
        Toast('您已提交过！')
      }
    },
    //填写个人信息
    saveUserInfo() {
      if (this.name != '' && this.hospital != '') {
        this.isWelcome = false
      } else {
        Toast('请填写个人信息')
      }
    },
    validateUserInfo() {
      if (this.name != '' && this.hospital != '') {
        return true
      } else {
        return false
      }
    },
    nameBlur() {
      setTimeout(() => {
        this.nameFocus = false
      }, 100)
    },
    clearHospital() {
      this.hospital = ''
    },
    clearName() {
      this.name = ''
    },
    hospitalBlur() {
      setTimeout(() => {
        this.hospitalFocus = false
      }, 100)
    },
    //回到页面底部
    bakToBot() {
      setTimeout(function() {
        var timer = null
        cancelAnimationFrame(timer)
        timer = requestAnimationFrame(function fn() {
          var oTop = document.body.scrollTop || document.documentElement.scrollTop
          var allTop = document.body.scrollHeight || document.documentElement.scrollHeight
          var canTop = document.documentElement.clientHeight
          if (oTop < allTop - canTop - 50) {
            document.body.scrollTop = document.documentElement.scrollTop = oTop + 50
            timer = requestAnimationFrame(fn)
          } else {
            cancelAnimationFrame(timer)
          }
        })
      }, 100)
    },
    //单选选中状态方法
    choose(index, idx) {
      if (this.isSubmit) {
        let content = this.option[idx]
        this.$set(this.optionRuslt, index, content)
      }
    }
  },
  watch: {
    //兼容安卓键盘弹出
    showHeight(val) {
      let that = this
      that.show2 = true
      if (that.docmHeight > val) {
        that.hidshow = false
      } else {
        that.hidshow = true
      }
    }
  }
}
</script>

<style lang="scss" scoped>
/* 个人信息 */
.content-box {
  /deep/.van-dialog {
    width: 100%;
  }
}
.information {
  margin: auto;
  width: 300px;
  // height: 370px;
  padding: 30px 22px 50px;

  .text-tip {
    color: red;
    padding-left: 30px;
    margin: 2px 0;
  }

  .hidden {
    visibility: hidden;
  }

  .title {
    height: 28px;
    font-size: 20px;
    font-weight: 500;
    color: rgba(51, 51, 51, 1);
    line-height: 28px;
    text-align: center;
    margin-bottom: 1px;
  }

  .sub-title {
    height: 17px;
    font-size: 12px;
    font-weight: 400;
    color: rgba(153, 153, 153, 1);
    line-height: 17px;
    text-align: center;
    margin-bottom: 21px;
  }

  .field-container {
    .field-wrap {
      height: 36px;
      border-radius: 18px;
      border: 0.5px solid #e3e3e3;
      padding-left: 30px;
      display: flex;
      align-items: center;
      margin-bottom: 16px;

      .van-cell {
        background: unset;
      }

      .dialog-name-img {
        width: 22px;
        height: 22px;
      }

      &:last-child {
        margin-bottom: 0px;
      }
    }
  }

  .dialog-button {
    width: 100%;
    border-radius: 18px;
    height: 36px;
    padding: 0;
    line-height: 36px;
    margin-top: 21px;
    background: #0080fe;
  }

  .dialog-tech {
    // width: 100%;
    position: absolute;
    font-size: 11px;
    color: #999;
    margin-top: 18px;
    display: block;
    left: 30%;
  }

  .disabled {
    background: #d8d8d8;
    pointer-events: none;
  }
}

/* 答题 */
.iviwe {
  text-align: center;
  // width: 100%;
  background: linear-gradient(1deg, rgba(1, 165, 81, 1) 0%, rgba(112, 184, 36, 1) 100%);
  padding: 15px;
  .content {
    background: rgba(255, 255, 255, 1);
    padding: 33px;
    .title {
      font-size: 20px;
      font-family: PingFangSC-Semibold, PingFang SC;
      font-weight: 600;
      color: rgba(51, 51, 51, 1);
    }
    .head {
      display: flex;
      justify-content: center;
      padding: 8px 0px 0px 0px;
      text-align: left;
      font-size: 20px;
      font-family: PingFangSC-Semibold, PingFang SC;
      font-weight: 600;
      color: rgba(51, 51, 51, 1);
    }
    .head-box {
      display: flex;
      justify-content: center;
      text-align: left;
      font-size: 20px;
      font-family: PingFangSC-Semibold, PingFang SC;
      font-weight: 600;
      color: rgba(51, 51, 51, 1);
    }
    .option-title {
      text-align: left;
      font-size: 16px;
      font-family: PingFangSC-Semibold, PingFang SC;
      font-weight: 600;
      color: rgba(51, 51, 51, 1);
      margin-top: 20px;
    }
    .option {
      .problem {
        line-height: 20px;
        margin: 18px 0px;
        font-size: 16px;
        font-family: PingFangSC-Medium, PingFang SC;
        font-weight: 500;
        color: rgba(51, 51, 51, 1);
        text-align: left;
      }
      .answer {
        .option-answer {
          font-size: 14px;
          font-family: PingFangSC-Regular, PingFang SC;
          font-weight: 400;
          color: rgba(51, 51, 51, 1);
          .queType-title {
            font-size: 14px;
            font-family: PingFangSC-Regular, PingFang SC;
            font-weight: 400;
            color: #f12433;
            text-align: left;
          }
          .answer-option {
            background: rgba(255, 255, 255, 1);
            border-radius: 4px;
            border: 1px solid rgba(153, 153, 153, 1);
            padding: 8px 13px;
            margin: 8px 0px;
            text-align: left;
          }
          .dis-answer-option {
            background: red;
            border-radius: 4px;
            border: 1px solid rgba(153, 153, 153, 1);
            padding: 8px 13px;
            margin: 8px 0px;
            text-align: left;
          }
        }
      }
      .van-radio__label {
        font-size: 14px;
        font-family: PingFangSC-Regular, PingFang SC;
        font-weight: 400;
        color: rgba(51, 51, 51, 1);
      }
    }
    .disSubmitColor {
      display: flex;
      justify-content: center;
      width: 100%;
      height: 42px;
      background: rgba(216, 216, 216, 1);
      border-radius: 5px;
      font-size: 18px;
      font-family: PingFangSC-Regular, PingFang SC;
      font-weight: 400;
      color: rgba(255, 255, 255, 1);
      margin-top: 37px;
      line-height: 42px;
    }
    .submitColor {
      display: flex;
      justify-content: center;
      width: 100%;
      height: 42px;
      background: linear-gradient(360deg, rgba(1, 165, 81, 1) 0%, rgba(112, 184, 36, 1) 100%);
      border-radius: 5px;
      font-size: 18px;
      font-family: PingFangSC-Regular, PingFang SC;
      font-weight: 400;
      color: rgba(255, 255, 255, 1);
      margin-top: 37px;
      line-height: 42px;
    }
  }
}

/* 答完题 */
.show-box {
  // display: flex;
  text-align: center;
  background: linear-gradient(360deg, rgba(1, 165, 81, 1) 0%, rgba(112, 184, 36, 1) 100%);
  padding: 8px;
  width: 100%;
  margin-top: 20px;
  border-radius: 10px;
  .submit-title {
    font-size: 20px;
    font-family: PingFangSC-Semibold, PingFang SC;
    font-weight: 600;
    color: #ffffff;
    margin: 30px 0px 0px 0px;
  }
  .gradeNum {
    font-size: 50px;
    font-family: PingFangSC-Semibold, PingFang SC;
    font-weight: 600;
    color: #ff7e00;
    margin-top: 10px;
    margin-bottom: 25px;
    span {
      font-size: 16px;
      margin-left: -10px;
    }
  }
}

/deep/ .van-dialog__content {
  text-align: center;
}
</style>