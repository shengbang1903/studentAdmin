<template>
  <div class="details">
    <span @click="returns" class="returns">
      <span class="iconfont icon-fanhui1"></span>
      返回
    </span>
    <el-divider class="headerStudent" content-position="left"
      >学生基本信息</el-divider
    >

    <header>
      <div class="avatar">
        <div class="demo-image__preview image">
          <el-image
            src="https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=1045179699,1407021187&fm=26&gp=0.jpg"
            :preview-src-list="[
              'https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=1045179699,1407021187&fm=26&gp=0.jpg',
            ]"
          ></el-image>
        </div>
      </div>
      <div class="name">
        <p>
          {{ obj.name }}
          <sub>{{ obj.sex }} · {{ obj.age }}</sub>
        </p>
      </div>
      <div class="basic">
        <ul>
          <li>
            <span class="iconfont icon-zhuzhi_chengshi"></span>
            <span>{{ obj.homeAddress }}</span>
          </li>
          <li>
            <span class="iconfont icon-yunpingtaiicon-"></span>
            <span>{{ obj.email }}</span>
          </li>
          <li>
            <span class="iconfont icon-shouji"></span>
            <span>{{ obj.phone }}</span>
          </li>
          <li>
            <span class="iconfont icon-credentials_icon"></span>
            <span>{{ obj.idCard }}</span>
          </li>
          <li>
            <span class="iconfont icon-yuanxibumen"></span>
            <span>{{ obj.system }}</span>
          </li>
          <li>
            <span class="iconfont icon-banji"></span>
            <span>{{ obj.class }}</span>
          </li>
          <li>
            <span class="iconfont icon-chushengriqi"></span>
            <span>{{ obj.birthday }}</span>
          </li>
          <li>
            <span class="iconfont icon-zhengzhimianmao"></span>
            <span>{{ obj.political }}</span>
          </li>
        </ul>
      </div>
    </header>
    <section>
      <h3>个人专业成绩</h3>

      <p>Personal professional performance</p>
      <div v-if="!this.isShow">
        <h2>暂无成绩</h2>
      </div>
      <v-chart v-else :options="polar" style="margin: 0 auto" />
    </section>
    <footer>
      <h3>个人平时表现明细</h3>
      <p>Personal performance details</p>
      <div style="text-align: center" v-show="obj.usallyScore.length === 0">
        <h2>暂无成绩</h2>
      </div>
      <ul>
        <li v-for="(item, indexn) in obj.usallyScore" :key="indexn">
          <div class="conent">
            <p class="header">
              分值变化 ：
              <span v-if="item[0].type === 1">+</span>
              <span v-if="item[0].type === 0">-</span>
              {{ item[0].fraction }}
            </p>
            <p class="dete">
              {{ item[0].time | timeModify(item[0].time) }}
              {{ item[0].time | tiemModitys(item[0].time) }}
            </p>
            <p class="sec">增扣分原因 ： {{ item[0].description }}</p>

            <div class="button">
              <el-button type="text" @click="trundrawer(indexn)">
                <el-button
                  type="primary"
                  icon="el-icon-edit"
                  circle
                ></el-button>
              </el-button>
              <!-- Form -->
              <el-dialog :visible.sync="dialogFormVisible">
                <div slot="footer" class="dialog-footer">
                  <div class="demo-drawer__content">
                    <div class="form">
                      <span class="posibinazhi">分值变化</span>
                      <p>
                        分值变化：
                        <span v-if="drawerobj.type === 1">+</span>
                        <span v-if="drawerobj.type === 0">-</span>
                        <input
                          type="text"
                          onkeyup="value=value.replace(/[^\d]/g,'')"
                          v-model="drawerobj.fraction"
                          @keydown.enter="sbmit"
                          class="inputjia"
                        />
                      </p>
                      <p>
                        增扣分原因：
                        <input
                          type="text"
                          @keydown.enter="sbmit"
                          v-model="drawerobj.description"
                        />
                      </p>
                    </div>
                    <div class="demo-drawer__footer">
                      <el-button @click="dialogFormVisible = false"
                        >取 消</el-button
                      >
                      <el-button
                        type="primary"
                        @click="sbmit"
                        :loading="loading"
                        >{{ loading ? "提交中 ..." : "确 定" }}</el-button
                      >
                    </div>
                  </div>
                </div>
              </el-dialog>
              <el-button type="text" @click="open(indexn)">
                <el-button
                  type="danger"
                  icon="el-icon-delete"
                  circle
                ></el-button>
              </el-button>
            </div>
          </div>
        </li>
      </ul>
      <p>综合分数：{{ Comprehensive }}</p>
    </footer>
  </div>
</template>
<script>
import moment from "moment";
import ECharts from "vue-echarts";
import "echarts/lib/chart/line";
import "echarts/lib/component/polar";
import "echarts/lib/component/tooltip";
import "echarts/lib/component/legend";
import "./iconfont.css";
export default {
  props: ["id"],
  components: { "v-chart": ECharts },
  data() {
    return {
      fullStack: [],
      quality: [],
      polar: {},
      dialog: false,
      loading: false,
      index: 0,
      drawerobj: {},
      isShow: true,
      obj: {
        name: "牛天罡",
        sex: "女",
        age: "98",
        birthday: "1954-01-01",
        idCard: "1304212000XXXXXXXX",
        homeAddress: "河北省邯郸市丛台区",
        class: "1909",
        system: "信息安全系",
        email: "1409410318@qq.com",
        political: "傻逼",
        phone: "15100404109",
        nation: "伊拉克族",
        usallyScore: [],
        professionScore: [],
      },
      dialogTableVisible: false,
      dialogFormVisible: false,
      form: {
        name: "",
        region: "",
        date1: "",
        date2: "",
        delivery: false,
        type: [],
        resource: "",
        desc: "",
      },
      formLabelWidth: "120px",
    };
  },
  computed: {
    Comprehensive() {
      let num = 0;
      for (let i = 0; i < this.obj.usallyScore.length; i++) {
        if (this.obj.usallyScore[i][0].type) {
          num += this.obj.usallyScore[i][0].fraction * 1;
        } else {
          num -= this.obj.usallyScore[i][0].fraction * 1;
        }
      }
      return num;
    },
  },
  methods: {
    returns() {
      this.$router.go(-1);
    },
    async findStudent() {
      const { data } = await this.$http.get(`/student/${this.id}`);
      this.obj = data.data;
      let fullStackNum = 0;
      let qualityNum = 0;
      this.obj.professionScore.map((item) => {
        //包含一个原理   类数组和纯数组
        this.fullStack.push(parseInt(item[0].fullStack));
        this.quality.push(parseInt(item[0].quality));
        item[0].fullStack == 0 && fullStackNum++;
        item[0].quality == 0 && qualityNum++;
      });
      if (fullStackNum == 4 && qualityNum == 4) {
        this.isShow = false;
      }

      this.polar = {
        tooltip: {
          trigger: "none",
          axisPointer: {
            type: "cross",
          },
        },
        legend: {
          data: ["2020 专业成绩", "2020 平时成绩"],
        },
        grid: {
          top: 70,
          bottom: 50,
        },
        xAxis: [
          {
            type: "category",
            axisTick: {
              alignWithLabel: true,
            },
            axisLine: {
              onZero: false,
              lineStyle: {
                color: "red",
              },
            },
            axisPointer: {
              label: {
                formatter: function(params) {
                  return (
                    "分数  " +
                    params.value +
                    (params.seriesData.length
                      ? "：" + params.seriesData[0].data
                      : "")
                  );
                },
              },
            },
            data: ["第一学期", "第二学期", "第三学期", "第四学期"],
          },
          {
            type: "category",
            axisTick: {
              alignWithLabel: true,
            },
            axisLine: {
              onZero: false,
              lineStyle: {
                color: "blue",
              },
            },
            axisPointer: {
              label: {
                formatter: function(params) {
                  return (
                    "分数  " +
                    params.value +
                    (params.seriesData.length
                      ? "：" + params.seriesData[0].data
                      : "")
                  );
                },
              },
            },
            data: ["第一学期", "第二学期", "第三学期", "第四学期"],
          },
        ],
        yAxis: [
          {
            type: "value",
          },
        ],
        series: [
          {
            name: "2020 专业成绩",
            type: "line",
            xAxisIndex: 1,
            smooth: true,
            data: this.fullStack,
          },
          {
            name: "2020 平时成绩",
            type: "line",
            smooth: true,
            data: this.quality,
          },
        ],
      };
      if (data.statusCode === 200) {
        this.$message.success("欢迎查看 " + this.obj.name + " 的信息");
      }
    },
    deteusally(index) {
      this.obj.usallyScore.splice(index, 1);
    },
    handleClose(done) {
      this.$confirm("确认关闭？")
        .then(() => {
          done();
        })
        .catch(() => {});
    },
    trundrawer(i) {
      if (this.$store.state.userInfo.role.purview[1] === 0) {
        this.$message.info("你没有访问权限s");
      } else {
        this.index = i;
        this.drawerobj = {
          time: this.obj.usallyScore[i][0].time,
          type: this.obj.usallyScore[i][0].type,
          fraction: this.obj.usallyScore[i][0].fraction,
          description: this.obj.usallyScore[i][0].description,
        };
        this.dialog = true;
        this.dialogFormVisible = true;
      }
    },
    cancelForm() {
      this.loading = false;
      this.dialog = false;
      clearTimeout(this.timer);
    },
    async sbmit() {
      this.loading = true;
      this.obj.usallyScore.splice(this.index, 1, [this.drawerobj]);
      await this.$http.put(`student/${this.id}`, this.obj);
      this.dialog = false;
      this.loading = false;
      this.$message.success("更改成功");
      this.dialogFormVisible = false;
    },
    open(index) {
      if (this.$store.state.userInfo.role.purview[2] === 0) {
        this.$message.info("你没有该权限");
      } else {
        this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        })
          .then(() => {
            this.$message({
              type: "success",
              message: "删除成功!",
            });
            this.deteusally(index);
          })
          .catch(() => {
            this.$message({
              type: "info",
              message: "已取消删除",
            });
          });
      }
    },
  },
  created() {
    if (!this.id) {
      this.$router.push("/");
    }
    this.findStudent();
  },
  filters: {
    timeModify: function(val) {
      var d = moment(new Date(parseInt(val))).format("YYYY-MM-DD");
      return d;
    },
    tiemModitys: function(val) {
      var d = moment(new Date(parseInt(val))).format("HH:mm:ss");
      return d;
    },
  },
};
</script>
<style lang="scss" scope>
.echarts {
  width: 95rem;
  height: 40rem;
}
ul {
  list-style: none;
}
#el-drawer__title {
  display: none;
}
.returns {
  margin-right: 30px;
  font-size: 25px;
  font-weight: bold;
  letter-spacing: 3px;
  cursor: pointer;
}
.details {
  .headerStudent {
    font-size: 25px;
  }
  width: 95%;
  margin: 0 auto;
  header {
    color: #fff;
    padding: 20px;
    margin-top: 100px;
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    background: #00bdbf;
    align-items: center;
    .image {
      width: 170px;
      height: 170px;
      border: #00bdbf 3px solid;
      border-radius: 50% !important;
      overflow: hidden;
      text-align: center;
      margin-top: -85px;
    }
    .name {
      font-size: 30px;
      margin: 15px 0;
      margin-top: 25px;
      sub {
        margin-left: 30px;
        font-size: 18px;
      }
    }
    .basic {
      ul {
        margin: 0 auto;
        li {
          display: inline-block;
          width: 250px;
          font-size: 18px;
          margin: 10px 0;
          text-align: left;
          span {
            padding: 0 4px;
          }
        }
      }
    }
  }
  section {
    margin: 80px 0;
    text-align: center;
    h3 {
      line-height: 80px;
      font-size: 25px;
    }
    ul {
      li {
        width: 150px;
        height: 150px;
        display: inline-block;

        // background: red;
        margin: 50px 50px;
        text-align: right;
        position: relative;
        .professionScore {
          color: #000;
          .profession {
            width: 60px;
            text-align: center;
            background: rgba(0, 180, 69, 0.5);
            position: absolute;
            bottom: -20px;
            border-radius: #000 1px solid;
            left: 0;
            span {
              position: absolute;
              top: -20px;
              left: 20px;
            }
          }
          .quality {
            width: 60px;
            text-align: center;
            background-color: rgba(15, 0, 151, 0.5);
            border-radius: #000 1px solid;
            position: absolute;
            bottom: -20px;
            left: 60px;
            span {
              position: absolute;
              top: -20px;
              left: 20px;
            }
          }
        }
      }
      .box {
        display: inline-block;
        margin: 0 20px;
        margin-top: 30px;
      }
      .pro {
        background: rgba(0, 180, 69, 0.5);
        width: 20px;
        height: 20px;
        margin-left: 500px;
      }
      .qua {
        background-color: rgba(15, 0, 151, 0.5);
        width: 20px;
        height: 20px;
      }
    }
  }
  .posibinazhi {
    position: absolute;
    top: 1.25rem;
    left: 1.25rem;
  }
  footer {
    background: rgb(255, 255, 255);
    color: #333;
    box-shadow: 0 0 15px rgba(0, 0, 0, 0.293);
    letter-spacing: 1px;
    padding: 20px;
    h3 {
      text-align: center;
      line-height: 60px;
      font-size: 25px;
    }
    p {
      text-align: center;
      line-height: 30px;
    }
    ul {
      width: 80%;
      margin: 0 auto;
      padding: 20px 0;
      li {
        border-left: 3px solid rgba(153, 0, 0, 0.74);
        padding-left: 120px;
        padding-top: 30px;
        position: relative;
        .conent {
          .form {
            h1 {
              text-align: left;
              margin: 30px 10px;
            }
            p {
              font-size: 18px;
              margin-bottom: 30px;
            }
          }
          .header {
            text-align: left;
            font-size: 20px;
            font-weight: bold;
          }
          .dete {
            text-align: left;
            line-height: 40px;
            font-size: 14px;
            color: #999;
            padding-left: 15px;
          }
          .sec {
            text-align: left;
            font-size: 18px;
            font-weight: bold;
          }
        }

        .button {
          text-align: right;
        }
        &::before {
          content: " ";
          display: block;
          background: rgb(153, 0, 0);
          width: 10px;
          height: 10px;
          border-radius: 50%;
          position: absolute;
          left: -6px;
          top: 45%;
        }
      }
    }
  }
}
</style>
