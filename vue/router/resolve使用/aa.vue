<template>
  <div class="tuoPu">
    <header>
      <multipleSelection
        :isShowManger="true"
        :isShowSubnet="true"
        :rightFindBtn="true"
        @update-data="update"
      ></multipleSelection>
      <ul class="navClass">
        <li>
          <Tooltip content="SDH默认拓扑设置" placement="top">
            <img
              v-if="skinStyle === 'dark'"
              src="../../assets/dark/深蓝色-搜索.svg"
              @click="tuopuSet"
              alt
            />
            <img
              v-else
              src="../../assets/light/蓝色-搜索.svg"
              @click="tuopuSet"
              alt
            />
          </Tooltip>
        </li>
        <li>
          <Tooltip content="保存位置" placement="top">
            <img
              v-if="skinStyle === 'dark'"
              src="../../assets/dark/深蓝色-保存.svg"
              @click="clickSave"
              alt
            />
            <img
              v-else
              src="../../assets/light/蓝色-保存.svg"
              @click="clickSave"
              alt
            />
          </Tooltip>
        </li>
        <li>
          <Tooltip content="纵览" placement="top">
            <img
              v-if="skinStyle === 'dark'"
              src="../../assets/dark/深蓝色-切换.svg"
              @click="overView"
              alt
            />
            <img
              v-else
              src="../../assets/light/蓝色-切换.svg"
              @click="overView"
              alt
            />
          </Tooltip>
        </li>
        <li class="searchInputclass">
          <Input
            placeholder="请输入查询条件..."
            style="flex: 350px"
            v-model="inputValue"
            @keyup.enter.native="findQuery"
          />
          <span class="icon_class--name" @click="findQuery"></span>
        </li>
        <li>
          <Button type="primary" @click="exportPicture">导出图片</Button>
        </li>
      </ul>
    </header>
    <section>
      <div id="canvas"></div>
      <div id="overview" v-show="isShowEyes"></div>
      <ul class="navBarClass">
        <!-- <li>
          <span>图元是否可拖动</span>
        </li>-->
        <li>
          <span style="display: inline-block; margin-right: 4px"
            >纤缆名称:</span
          >
          <i-switch v-model="switch2" @on-change="changeLineName" />
        </li>
        <li>
          <Checkbox
            v-model="isShowLine"
            @on-change="changeLineShow"
            size="small"
            >纤缆</Checkbox
          >
        </li>
        <li>
          <Checkbox
            v-model="isShowPortect"
            @on-change="changeLineProtect"
            size="small"
            >保护</Checkbox
          >
        </li>
        <li>
          <Checkbox
            v-model="isShowEyes"
            @on-change="changeEyesShow"
            size="small"
            >鹰眼</Checkbox
          >
        </li>
      </ul>
    </section>
    <panelContent ref="panelRef"></panelContent>
    <Modal
      title="SDH默认拓扑设置"
      v-model="modal"
      :closable="false"
      :mask-closable="false"
      @on-ok="ok"
      @on-cancel="cancel"
      width="360"
      draggable
    >
      <div>
        <ul style="display: flex; flex-direction: column; text-align: center">
          <li>
            <span>网管:</span>
            <Select
              v-model="sdhMangerId"
              style="width: 200px"
              @on-change="setsdhNeManage"
              size="small"
              transfer
              multiple
              class="multiple_class"
            >
              <Option
                v-for="(item, index) in sdhNeManger"
                :value="item.neManageId"
                :key="index"
                >{{ item.neManageName }}</Option
              >
            </Select>
          </li>
          <li style="margin-top: 10px">
            <span>子网:</span>
            <Select
              v-model="sdhSubnetId"
              style="width: 200px"
              size="small"
              transfer
            >
              <Option
                v-for="(item, index) in sdhSubNet"
                :value="item.subNetId"
                :key="index"
                >{{ item.subNetName }}</Option
              >
            </Select>
          </li>
        </ul>
      </div>
    </Modal>
  </div>
</template>

<script>
import store from "@/store/index";
import multipleSelection from "../common/component/multipleSelection";
import deviceIco from "@/components/common/js/deviceIco";
import { recordLoc } from "@/components/common/js/tuopusave";
import "@/components/common/js/overview";
import "@/components/common/js/PopupMenu";
import panelContent from "./jtopo/equipContain";
export default {
  data() {
    return {
      tuopuGraph: null,
      overview: null,
      graphNode: {},
      waveObj: {},
      line: {},
      subnetObj: {},
      mangerObj: {},
      projectLine: {},
      deviceIco: deviceIco,
      modal: false,
      sdhNeManger: [],
      sdhMangerId: [],
      sdhSubNet: [],
      sdhSubnetId: "",
      cancelMangerIdValue: [],
      //一个容器存储 SubnetIdValue
      cancelSubnetIdValue: "",

      inputValue: "",
      moveObject: {},
      cishu: 0,
      graghModel: null,

      switch2: false,
      isShowLine: true,
      isShowPortect: false,
      isShowSelect: false,
      isShowEyes: false,
      protectneManageIds: "",
      neManageIds: "",
      oColor: "",
      fontColor: "",
      Yset_1: require("../../../public/static/icon/Yset_1_dark.svg"),
    };
  },
  components: {
    multipleSelection,
    panelContent,
  },
  watch: {
    skinStyle: {
      handler(v) {
        this.oColor = v == "dark" ? "#01CBE7" : "black";
        this.fontColor = v == "dark" ? "white" : "black";
        if (v == "dark") {
          this.Yset_1 = require("../../../public/static/icon/Yset_1_dark.svg");
        } else {
          this.Yset_1 = require("../../../public/static/icon/Yset_1_light.svg");
        }

        for (let key in this.line) {
          this.line[key].setStyle(Q.Styles.EDGE_COLOR, this.oColor);
          this.line[key].setStyle(
            Q.Styles.EDGE_BUNDLE_LABEL_COLOR,
            this.oColor
          );
        }
        for (let key in this.subnetObj) {
          this.subnetObj[key].setStyle(Q.Styles.LABEL_COLOR, this.fontColor);
        }
        for (let key in this.mangerObj) {
          this.mangerObj[key].setStyle(Q.Styles.LABEL_COLOR, this.fontColor);
        }
      },
    },
  },
  computed: {
    skinStyle() {
      return store.state.skinStyle;
    },
    defaultBureauId() {
      return store.state.defaultBureauId || "";
    },
  },
  mounted() {
    let _that = this;
    let eyes = document.getElementById("overview");
    this.tuopuGraph = new Q.Graph("canvas");

    let styles = {};
    styles[Q.Styles.SELECTION_COLOR] = "#E55B00";
    // styles[Q.Styles.SELECTION_SHADOW_BLUR] = 7;
    this.tuopuGraph.styles = styles;

    //双击网元
    this.tuopuGraph.ondblclick = (evt) => {
      var oNode = this.tuopuGraph.getElementByMouseEvent(evt);
      if (oNode && oNode.moveObj === "网元") {
        let paramId = {
          neManageId: oNode.neManageId,
          subNetId: oNode.subNetId,
          deviceId: oNode.deviceId,
          deviceType: oNode.deviceType,
          name: oNode.name,
        };
        this.$refs.panelRef.panelDemo(paramId);
      }
    };

    //右键弹框
    this.tuopuGraph.popupmenu = new Q.PopupMenu();
    this.tuopuGraph.popupmenu.getMenuItems = function (graph, data, evt) {
      _that.$nextTick(() => {
        if (_that.skinStyle == "dark") {
          $(".dropdown-menu").addClass("dark_dropdown_menu");
          if ($(".dropdown-menu").hasClass("light_dropdown_menu")) {
            $(".dropdown-menu").removeClass("light_dropdown_menu");
          }
        } else {
          $(".dropdown-menu").addClass("light_dropdown_menu");
          if ($(".dropdown-menu").hasClass("dark_dropdown_menu")) {
            $(".dropdown-menu").removeClass("dark_dropdown_menu");
          }
        }
      });

      if (data) {
        let arr = [];
        let oStr = "";
        graph.selectionModel.datas.forEach((item) => {
          if (item.deviceId) {
            arr = arr.concat(item.deviceId);
          }
        });
        if (arr.length <= 0) {
          oStr = "('')";
        } else {
          oStr = _that.detailIdArr(arr);
        }
        //网元面板
        //可以根据data判断，返回不同的菜单
        if (data.moveObj && data.moveObj == "网元") {
          return [
            {
              text: "删除",
              action: function () {
                // if (confirm("确定删除图元")) {
                //   // graph.removeElement(data);
                // }
                _that.$Modal.confirm({
                  title: "删除",
                  content: "<p>确定删除图元吗?</p>",
                  width: 300,
                  onOk: () => {
                    axios
                      .post(
                        BASE_URL +
                          "a/transperfor/neparainfo/neParaInfo/checkFiberInfo",
                        {
                          deviceIds: oStr,
                        }
                      )
                      .then((res) => {
                        if (res.data) {
                          _that.$Message.warning("网元被线别占用,暂不能删除");
                          return;
                        } else {
                          axios
                            .post(
                              BASE_URL +
                                "a/transperfor/neparainfo/neParaInfo/updateNeParaInfo",
                              {
                                deviceIds: oStr,
                                delFlag: "SC",
                              }
                            )
                            .then((res) => {
                              graph.graphModel.remove(nodeArr);
                              _that.$Message.info(res.data);
                            });
                        }
                      });
                  },
                  onCancel: () => {},
                });
              },
            },
            {
              text: "面板",
              action: function () {
                //面板
                // Q.prompt("Input Element Name", data.name || "", function(
                //   name
                // ) {});
                let paramId = {
                  neManageId: data.neManageId,
                  subNetId: data.subNetId,
                  deviceId: data.deviceId,
                  deviceType: data.deviceType,
                  name: data.name,
                };
                _that.$refs.panelRef.panelDemo(paramId);
              },
            },
          ];
        }
        if (
          data.fiberId &&
          (data.getEdgeBundle().length == 1 || data.getEdgeBundle().expanded)
        ) {
          return [
            {
              text: "光纤删除",
              action: function () {
                _that.$Modal.confirm({
                  title: "删除",
                  content: "<p>确定删除光纤?</p>",
                  width: 300,
                  onOk: () => {
                    axios
                      .post(
                        BASE_URL + "a/fiberandcable/newFiber/deleteFibers",
                        {
                          fiberIds: fiberIdArr,
                        }
                      )
                      .then((res) => {
                        if (res.data.code == "1") {
                          for (let i = 0; i < lineArr.length; i++) {
                            graph.graphModel.remove(lineArr[i]);
                          }
                          _that.$Message.success(res.data.msg);
                        } else {
                          Message.warning(res.data.msg);
                        }
                      })
                      .catch((error) => {
                        //console.log(error);
                      });
                  },
                });
              },
            },
            {
              text: "相关业务",
              action: function () {
                // fiberId: "7d030bf0-5d73-4e65-bde9-1ed1472b8d24"
                // srcNeId: "8d7c819c6ce44139816705b51e413d48"
                // srcBoardId: "2110731457521013"
                // srcPortId: "2110731457521013002002"
                axios
                  .post(BASE_URL + "a/relevantservice/init/queryRelevantWP", {
                    fiberId: data.fiberId,
                  })
                  .then((res) => {
                    if (res.data.code == 1) {
                      let { href } = _that.$router.resolve({
                        path: "/sdhPath",
                        query: {
                          srcNeId: data.srcNeId ? data.srcNeId : "",
                          srcBoardId: data.srcBoardId ? data.srcBoardId : "",
                          srcPortId: data.srcPortId ? data.srcPortId : "",
                          sdhInfoId: res.data.dataList.join(","),
                        },
                      });
                      window.open(href, "_blank");
                    } else {
                      _that.$Message.warning(res.data.msg);
                      return;
                    }
                  });
              },
            },
            {
              text: "相关工作业务",
              action: function () {
                axios
                  .post(BASE_URL + "a/relevantservice/init/queryRelevantW", {
                    fiberId: data.fiberId,
                  })
                  .then((res) => {
                    if (res.data.code == 1) {
                      let { href } = _that.$router.resolve({
                        path: "/sdhPath",
                        query: {
                          srcNeId: data.srcNeId ? data.srcNeId : "",
                          srcBoardId: data.srcBoardId ? data.srcBoardId : "",
                          srcPortId: data.srcPortId ? data.srcPortId : "",
                          sdhInfoId: res.data.dataList.join(","),
                        },
                      });
                      window.open(href, "_blank");
                    } else {
                      _that.$Message.warning(res.data.msg);
                      return;
                    }
                  });
              },
            },
          ];
        }
      }
    };
    //鹰眼
    this.overview = new Q.Overview(eyes);
    this.changeEyesShow(this.isShowEyes);

    Q.Defaults.EDGE_BUNDLE_EXPANDED = false;
    //保存位置
    this.tuopuGraph.addCustomInteraction({
      enddrag: (e, m) => {
        this.moveObject = recordLoc(m);
      },
    });

    //根据线颜色排列显示顺序
    if (!Q.__customEdgeBundle) {
      //避免多次重写
      Q.__customEdgeBundle = true;
      Q.EdgeBundle.prototype.sortBindingEdges = function (edges) {
        edges.sort(function (edge1, edge2) {
          if (
            edge1.getStyle(Q.Styles.EDGE_COLOR) == "blue" ||
            edge1.getStyle(Q.Styles.EDGE_COLOR) == "pink" ||
            edge1.getStyle(Q.Styles.EDGE_COLOR) == "#00E676"
          ) {
            return -1;
          } else {
            return 1;
          }
        });
      };
    }
    this.oColor = this.skinStyle == "dark" ? "#01CBE7" : "black";
    this.fontColor = this.skinStyle == "dark" ? "white" : "black";
    //单击保护线别效果
    this.tuopuGraph.addCustomInteraction({
      onclick: (evt) => {
        let ui = this.tuopuGraph.getUIByMouseEvent(evt);
        let model = this.tuopuGraph.graphModel;
        let selectionModel = model.selectionModel;
        if (ui) {
          if (ui.$data && ui.$data.psnId) {
            for (let key in this.projectLine) {
              if (this.projectLine[key].psnId == ui.$data.psnId) {
                selectionModel.select(this.projectLine[key]);
              } else {
                selectionModel.unselect(this.projectLine[key]);
              }
            }
          }
        }
      },
    });
  },
  methods: {
    update(v) {
      let neManageIds = "";
      let subNetIds = "";
      let neMangerArr = [];
      let newneMangerArr = [];
      if (v.neManageId.length <= 0) {
        neManageIds = "";
      } else {
        neManageIds = this.detailIdArr(v.neManageId);
      }
      this.neManageIds = neManageIds;
      //保护Ids
      if (v.neManageId.length == 0) {
        this.protectneManageIds = "";
      } else if (v.neManageId.length == 1) {
        this.protectneManageIds = v.neManageId[0];
      } else {
        // //console.log("v.neManageId",v.neManageId);
        this.protectneManageIds = v.neManageId.join(",");
      }

      if (v.subNetId.length <= 0) {
        subNetIds = "";
      } else if (v.subNetId.length == 1 && v.subNetId[0] == "") {
        subNetIds = "";
      } else {
        subNetIds = this.detailIdArr(v.subNetId);
      }
      if (v.neMangerArr.length <= 0) {
        neMangerArr = [];
      } else {
        neMangerArr = v.neMangerArr;
      }

      for (let i = 0; i < neMangerArr.length; i++) {
        for (let j = 0; j < v.neManageId.length; j++) {
          if (neMangerArr[i].neManageId == v.neManageId[j]) {
            newneMangerArr = newneMangerArr.concat([neMangerArr[i]]);
          }
        }
      }
      if (Object.keys(this.tuopuGraph).length) {
        // +2 问题修复
        if (Object.keys(this.graphNode).length) {
          for (let key in this.graphNode) {
            this.tuopuGraph.removeElement(this.graphNode[key]);
            delete this.graphNode[key];
          }
        }
        //清除线 纤缆 显示/隐藏
        if (Object.keys(this.line).length) {
          for (let key in this.line) {
            this.tuopuGraph.removeElement(this.line[key]);
            delete this.line[key];
          }
        }
        //清除画布 异步问题
        this.tuopuGraph.clear();
      }

      this.commonParams.getTopMap(neManageIds, subNetIds).then((res) => {
        this.createNode(res.data);
        this.createWavenode(res.data);
        this.createSubnet(res.data);
        this.createManager(res.data, newneMangerArr);
        if (this.isShowLine) {
          this.commonParams.getFiber(neManageIds).then((res) => {
            this.createLine(res.data);
          });
        }
        if (this.isShowPortect) {
          this.changeLineProtect(true);
        }
      });
    },

    createNode(dataList) {
      //网元
      for (let i = 0; i < dataList.net.length; i++) {
        const netItem = dataList.net[i];
        var ico = "";
        ico = "osn1500-4.svg";
        let grayIcon = "";
        for (var r = 0; r < this.deviceIco.length; r++) {
          if (netItem.deviceType == this.deviceIco[r].type) {
            if (this.deviceIco[r].ico.indexOf(".") != -1) {
              ico = this.deviceIco[r].ico;
            } else {
              ico = this.deviceIco[r].ico + "-1.svg";
              grayIcon = this.deviceIco[r].ico + "-6.svg";
            }
          }
        }
        //Qunee创建单个网元
        this.graphNode[netItem.deviceId] = this.tuopuGraph.createNode(
          netItem.neName,
          parseInt(netItem.xAxes),
          parseInt(netItem.yAxes)
        );

        for (let m = 0; m < dataList.oNePara.length; m++) {
          if (netItem.oNeId && netItem.oNeId == dataList.oNePara[m].oNeId) {
            //网元 绑定光网元坐标
            this.graphNode[netItem.deviceId].x = parseInt(
              dataList.oNePara[m].xAxes
            );
            this.graphNode[netItem.deviceId].y = parseInt(
              dataList.oNePara[m].yAxes
            );
          }
        }

        //给每一个网元添加deviceId属性
        this.graphNode[netItem.deviceId].deviceId = netItem.deviceId;
        this.graphNode[netItem.deviceId].oNeId = netItem.oNeId;
        this.graphNode[netItem.deviceId].subNetId = netItem.subNetId;
        this.graphNode[netItem.deviceId].neManageId = netItem.neManageId;
        this.graphNode[netItem.deviceId].deviceType = netItem.deviceType;
        this.graphNode[netItem.deviceId].moveObj = "网元";
        this.graphNode[netItem.deviceId].currentDeviceStatus =
          netItem.currentDeviceStatus ? netItem.currentDeviceStatus : "";

        if (netItem.preCfg == "1") {
          this.addNodeFlag(this.graphNode[netItem.deviceId]);
        } else {
        }

        this.graphNode[netItem.deviceId].setStyle(
          Q.Styles.LABEL_COLOR,
          "black"
        );
        this.graphNode[netItem.deviceId].setStyle(
          Q.Styles.LABEL_BACKGROUND_COLOR,
          "white"
        );
        this.graphNode[netItem.deviceId].size = {
          width: 64,
          height: 64,
        };
        this.graphNode[netItem.deviceId].image = "static/equipment/" + ico;
        if (
          this.graphNode[netItem.deviceId].currentDeviceStatus &&
          this.graphNode[netItem.deviceId].currentDeviceStatus ==
            "CS_UNAVAILABLE"
        ) {
          this.graphNode[netItem.deviceId].image =
            "static/equipment/" + grayIcon;
        }
      }
      this.overView();
    },
    createLine(dataList) {
      for (let j = 0; j < dataList.length; j++) {
        const listItem = dataList[j];
        if (
          this.graphNode[listItem.snkNeId] &&
          this.graphNode[listItem.srcNeId]
        ) {
          this.line[listItem.fiberId] = this.tuopuGraph.createEdge(
            listItem.name,
            this.graphNode[listItem.snkNeId],
            this.graphNode[listItem.srcNeId]
          );
          //线别名称 初始状态为不显示
          this.line[listItem.fiberId].setStyle(Q.Styles.LABEL_VISIBLE, false);
          if (this.line[listItem.fiberId]) {
            this.line[listItem.fiberId].tooltip =
              "源端：" +
              (listItem.src ? listItem.src : "") +
              "<br/>宿端：" +
              (listItem.snk ? listItem.snk : "");

            this.line[listItem.fiberId].setStyle(
              Q.Styles.EDGE_BUNDLE_TYPE,
              Q.Consts.EDGE_BUNDLE_TYPE_PARALLEL
            );
            this.line[listItem.fiberId].fiberId = listItem.fiberId;
            this.line[listItem.fiberId].srcNeId = listItem.srcNeId;
            this.line[listItem.fiberId].srcBoardId = listItem.srcBoardId;
            this.line[listItem.fiberId].srcPortId = listItem.srcPortId;
            //线上字体颜色
            this.line[listItem.fiberId].setStyle(Q.Styles.LABEL_COLOR, "black");
            //线上字体背景颜色
            this.line[listItem.fiberId].setStyle(
              Q.Styles.LABEL_BACKGROUND_COLOR,
              "rgba(255, 255, 255, 0.5)"
            );
            //线上是否显示箭头
            this.line[listItem.fiberId].setStyle(Q.Styles.ARROW_TO, false);
            this.line[listItem.fiberId].setStyle(
              Q.Styles.EDGE_BUNDLE_LABEL_COLOR,
              this.oColor
            );
            //线别颜色
            this.line[listItem.fiberId].setStyle(
              Q.Styles.EDGE_COLOR,
              this.oColor
            );
            //线别宽度
            this.line[listItem.fiberId].setStyle(Q.Styles.EDGE_WIDTH, 1);
          }
        }
      }
    },

    createWavenode(dataList) {
      if (!dataList.oNePara.length) return;
      for (var i = 0; i < dataList.oNePara.length; i++) {
        const ele = dataList.oNePara[i];
        if (ele.oNeName) {
          this.waveObj[ele.oNeName] = this.addSymbol(
            false,
            ele.oNeName,
            parseInt(ele.xAxes),
            parseInt(ele.yAxes)
          );
          //添加subNetId属性
          this.waveObj[ele.oNeName].subNetId = ele.subNetId;
          this.waveObj[ele.oNeName].oNeId = ele.oNeId;
          this.waveObj[ele.oNeName].moveObj = "光网元";
          this.waveObj[ele.oNeName].setStyle(
            Q.Styles.GROUP_BACKGROUND_COLOR,
            "white"
          );

          this.waveObj[ele.oNeName].setStyle(Q.Styles.LABEL_COLOR, "black");
          this.waveObj[ele.oNeName].setStyle(
            Q.Styles.LABEL_BACKGROUND_COLOR,
            "white"
          );
          for (var key in this.graphNode) {
            if (this.waveObj[ele.oNeName].oNeId === this.graphNode[key].oNeId) {
              this.waveObj[ele.oNeName].addChild(this.graphNode[key]);
            }
          }
        }

        if (ele.oNeType === "OADM") {
          this.waveObj[ele.oNeName].image = "static/equipment/OADM-1.svg";
        } else if (ele.oNeType === "OLA") {
          this.waveObj[ele.oNeName].image = "static/equipment/OLA-1.svg";
        } else if (ele.oNeType === "OTM") {
          this.waveObj[ele.oNeName].image = "static/equipment/OTM-1.svg";
        }

        if (ele.oNeType) {
          this.waveObj[ele.oNeName].size = { width: 70, height: 70 };
        }
      }
    },

    createSubnet(dataList) {
      for (var i = 0; i < dataList.subNet.length; i++) {
        const listSubNetItem = dataList.subNet[i];
        if (listSubNetItem.subNetId) {
          this.subnetObj[listSubNetItem.subNetName] =
            this.tuopuGraph.createGroup(
              listSubNetItem.subNetName,
              parseInt(listSubNetItem.xAxes),
              parseInt(listSubNetItem.yAxes)
            );

          //前端设置 拓扑为 收起状态
          this.subnetObj[listSubNetItem.subNetName].expanded = false;

          // 图标
          this.subnetObj[listSubNetItem.subNetName].image =
            "static/equipment/subnet.svg";
          this.subnetObj[listSubNetItem.subNetName].size = {
            width: 70,
            height: 70,
          };

          this.subnetObj[listSubNetItem.subNetName].setStyle(
            Q.Styles.GROUP_STROKE,
            2
          );
          this.subnetObj[listSubNetItem.subNetName].setStyle(
            Q.Styles.GROUP_STROKE_STYLE,
            "#9966ff"
          );
          this.subnetObj[listSubNetItem.subNetName].setStyle(
            Q.Styles.GROUP_STROKE_LINE_DASH,
            [3, 2]
          );
        }
        //光网元
        for (var key in this.waveObj) {
          if (this.waveObj[key].subNetId === listSubNetItem.subNetId) {
            this.subnetObj[listSubNetItem.subNetName].addChild(
              this.waveObj[key]
            );
          }
        }
        //不属于光网元 直接属于子网
        for (var key in this.graphNode) {
          if (
            !this.graphNode[key].oNeId &&
            this.graphNode[key].subNetId === listSubNetItem.subNetId
          ) {
            this.subnetObj[listSubNetItem.subNetName].addChild(
              this.graphNode[key]
            );
          }
        }
        this.subnetObj[listSubNetItem.subNetName].subNetId =
          listSubNetItem.subNetId;

        this.subnetObj[listSubNetItem.subNetName].neManageId =
          listSubNetItem.neManageId;

        this.subnetObj[listSubNetItem.subNetName].moveObj = "子网";
        this.subnetObj[listSubNetItem.subNetName].setStyle(
          Q.Styles.GROUP_BACKGROUND_COLOR,
          false
        );
        this.subnetObj[listSubNetItem.subNetName].setStyle(
          Q.Styles.LABEL_COLOR,
          this.fontColor
        );
      }
    },
    createManager(dataList, manger) {
      for (let i = 0; i < manger.length; i++) {
        const mangerItem = manger[i];
        this.mangerObj[mangerItem.neManageName] = this.tuopuGraph.createGroup(
          mangerItem.neManageName
        );
        this.mangerObj[mangerItem.neManageName].neManageId =
          mangerItem.neManageId;
        //子网添加入网管
        for (var key in this.subnetObj) {
          if (
            this.mangerObj[mangerItem.neManageName].neManageId ===
            this.subnetObj[key].neManageId
          ) {
            this.mangerObj[mangerItem.neManageName].addChild(
              this.subnetObj[key]
            );
          }
        }
        //网元不属于子网 不属于光网元 属于网管
        for (var key in this.graphNode) {
          if (
            !this.graphNode[key].oNeId &&
            !this.graphNode[key].subNetId &&
            this.graphNode[key].neManageId ===
              this.mangerObj[mangerItem.neManageName].neManageId
          ) {
            this.mangerObj[mangerItem.neManageName].addChild(
              this.graphNode[key]
            );
          }
        }

        this.mangerObj[mangerItem.neManageName].moveObj = "网管";
        this.mangerObj[mangerItem.neManageName].setStyle(
          Q.Styles.GROUP_STROKE,
          3
        );
        this.mangerObj[mangerItem.neManageName].setStyle(
          Q.Styles.GROUP_STROKE_STYLE,
          "orange"
        );
        this.mangerObj[mangerItem.neManageName].setStyle(
          Q.Styles.GROUP_STROKE_LINE_DASH,
          [3, 2]
        );
        this.mangerObj[mangerItem.neManageName].setStyle(
          Q.Styles.GROUP_BACKGROUND_COLOR,
          false
        );
        this.mangerObj[mangerItem.neManageName].setStyle(
          Q.Styles.LABEL_COLOR,
          this.fontColor
        );
      }
      this.graghModel = this.tuopuGraph.createGroup("");
      this.graghModel.padding = new Q.Insets(50, 50);
      this.graghModel.setStyle(Q.Styles.GROUP_BACKGROUND_COLOR, false);
      this.graghModel.setStyle(Q.Styles.GROUP_STROKE_STYLE, "rgba(0,0,0,0)");
      for (let key in this.mangerObj) {
        this.graghModel.addChild(this.mangerObj[key]);
      }
    },

    detailIdArr(arr) {
      let _adrobj = JSON.stringify(arr).replace(/\"/g, "'");
      let _adrobj2 = _adrobj.replace(/\[/g, "(");
      let _adrobj3 = _adrobj2.replace(/\]/g, ")");
      return _adrobj3;
    },
    overView() {
      this.tuopuGraph.callLater(() => {
        this.tuopuGraph.zoomToOverview();
      });
    },
    //添加 - +
    addSymbol(expanded, name, x, y) {
      expanded = expanded !== false;
      let group = this.tuopuGraph.createGroup(name, x, y);
      group.expanded = expanded;
      let groupHandle = new Q.LabelUI(expanded ? "-" : "+");
      groupHandle.backgroundColor = "#2898E0";
      groupHandle.color = "#FFF";
      groupHandle.padding = new Q.Insets(0, 4);
      groupHandle.borderRadius = 0;
      groupHandle.position = Q.Position.RIGHT_TOP;
      groupHandle.anchorPosition = Q.Position.LEFT_TOP;
      groupHandle.type = "GroupHandle";
      groupHandle.reverseExpanded = function (evt) {
        let g = this.parent.data;
        g.expanded = !g.expanded;
      };
      group.addUI(groupHandle, {
        property: "expanded",
        callback: function (value, ui) {
          ui.data = value ? "-" : "+";
        },
      });
      return group;
    },

    tuopuSet() {
      this.modal = true;
      this.commonParams
        .getSysParaPageGroupLink("sdhHomePageGroupLink")
        .then((res) => {
          //多网管
          if (res.data.defaultValue.includes(",")) {
            this.sdhMangerId = res.data.defaultValue.split(",");

            this.cancelMangerIdValue = res.data.defaultValue.split(",");
            // 单个网管
          } else {
            this.sdhMangerId = [res.data.defaultValue];
            this.cancelMangerIdValue = [res.data.defaultValue];
          }

          this.sdhSubnetId = res.data.maxLimit;
          this.cancelSubnetIdValue = res.data.maxLimit;

          this.commonParams.neManageList(this.defaultBureauId).then((res) => {
            this.sdhNeManger = res;
          });
        })
        .catch((err) => {
          this.$Message.warning("配置错误");
          return;
        });
    },
    ok() {
      let arr = this.sdhMangerId;
      let aa = "";
      if (!arr.length) {
        aa = "";
      } else if (arr.length == 1) {
        aa = arr[0];
      } else if (arr.length >= 2) {
        aa = arr.join(",");
      }
      this.modal = false;
      axios
        .post(BASE_URL + "a/transperfor/homepage/homePage/updateSysPara", {
          defaultValue: aa,
          maxLimit: this.sdhSubnetId,
          paraCode: "sdhHomePageGroupLink",
        })
        .then((res) => {
          this.cancelMangerIdValue = this.sdhMangerId;
          this.cancelSubnetIdValue = this.sdhSubnetId;

          this.$Message.success(res.data);
          return;
        })
        .catch((err) => {
          this.$Message.warning("配置错误");
          return;
        });
    },
    cancel() {
      this.modal = false;
      this.sdhMangerId = this.cancelMangerIdValue;
      this.sdhSubnetId = this.cancelSubnetIdValue;
    },
    setsdhNeManage(v) {
      this.sdhSubNet = [{ subNetId: "全部子网", subNetName: "全部子网" }];
      if (v.length >= 2) {
        this.sdhSubnetId = "全部子网";
      } else {
        this.getsdhSubNet();
      }
    },
    getsdhSubNet() {
      let str = "";
      if (this.sdhMangerId.length == 1) {
        str = this.sdhMangerId[0];
      }

      this.commonParams.subNetList(str).then((res) => {
        this.sdhSubNet = this.sdhSubNet.concat(res);
        //获取最初赋值
        this.sdhSubnetId = this.cancelSubnetIdValue;
      });
    },

    clickSave() {
      if (!Object.keys(this.moveObject).length) {
        this.$Message.warning("请选择保存对象");
        return;
      }
      axios
        .post(
          BASE_URL + "a/transperfor/topmap/topMap/saveTopMap",
          this.moveObject
        )
        .then((res) => {
          this.$Message.info(res.data);
        });
    },
    findQuery() {
      if (!this.inputValue) {
        this.$Message.warning("请输入内容");
        return;
      }
      let graphNodeArr = Object.values(this.graphNode);
      let result = [];
      let jdian = [];
      let gdian = [];
      let zdian = [];
      let wdian = [];
      //带有（）的形式
      let regF = this.inputValue.replace(/\(/g, "\\(").replace(/\)/g, "\\)");
      let reg = new RegExp(regF, "i");
      this.tuopuGraph.forEach((e) => {
        if (this.tuopuGraph.isVisible(e) && e.name && reg.test(e.name)) {
          if (e.location) {
            result.push(e.location);
            if (e.moveObj === "网元") {
              jdian.push(e.id);
            } else if (e.moveObj === "光网元") {
              gdian.push(e.id);
            } else if (e.moveObj === "子网") {
              zdian.push(e.id);
            } else if (e.moveObj === "网管") {
              wdian.push(e.id);
            }
          }
        }
      });
      if (this.cishu > result.length - 1) {
        this.cishu = 1;
      } else {
        this.cishu++;
      }
      this.tuopuGraph.callLater(() => {
        this.tuopuGraph.centerTo(
          result[this.cishu - 1].x,
          result[this.cishu - 1].y,
          1.5
        );
      });
      var index = null;
      var model = this.tuopuGraph.graphModel;
      var selectionModel = model.selectionModel;
      for (var i = 0; i < graphNodeArr.length; i++) {
        if (graphNodeArr[i].id == jdian[this.cishu - 1]) {
          index = i;
          selectionModel.select(graphNodeArr[i]);
        } else {
          selectionModel.unselect(graphNodeArr[i]);
        }
      }
      for (var key in this.waveObj) {
        if (this.waveObj[key].id === gdian[this.cishu - 1]) {
          selectionModel.select(this.waveObj[key]);
        } else {
          selectionModel.unselect(this.waveObj[key]);
        }
      }
      for (var key in this.subnetObj) {
        if (this.subnetObj[key].id === zdian[this.cishu - 1]) {
          selectionModel.select(this.subnetObj[key]);
        } else {
          selectionModel.unselect(this.subnetObj[key]);
        }
      }
      for (var key in this.mangerObj) {
        if (this.mangerObj[key].id === wdian[this.cishu - 1]) {
          selectionModel.select(this.mangerObj[key]);
        } else {
          selectionModel.unselect(this.mangerObj[key]);
        }
      }
      if (!index) {
        this.$Message.warning("暂无此网元");
        return;
      }
    },
    exportPicture() {
      if (!this.graghModel) {
        this.$Message.warning("请选择导出内容");
        return;
      }
      //导出图片
      this.graghModel.setStyle(Q.Styles.GROUP_BACKGROUND_COLOR, "#091541");
      let imageInfo = this.tuopuGraph.exportImage(1);
      if (!imageInfo || !imageInfo.data) {
        return false;
      }
      this.downloadFile(
        "拓扑图 - " + imageInfo.width + "x" + imageInfo.height + ".png",
        imageInfo.data
      );
      this.graghModel.setStyle(Q.Styles.GROUP_BACKGROUND_COLOR, false);
    },
    downloadFile(fileName, content) {
      let aLink = document.createElement("a");
      let blob = this.base64ToBlob(content); // new Blob([content]);

      let evt = document.createEvent("HTMLEvents");
      evt.initEvent("click", true, true); // initEvent 不加后两个参数在FF下会报错  事件类型，是否冒泡，是否阻止浏览器的默认行为
      aLink.download = fileName;
      aLink.href = URL.createObjectURL(blob);
      aLink.dispatchEvent(
        new MouseEvent("click", {
          bubbles: true,
          cancelable: true,
          view: window,
        })
      ); // 兼容火狐
    },
    base64ToBlob(code) {
      let parts = code.split(";base64,");
      let contentType = parts[0].split(":")[1];
      let raw = window.atob(parts[1]);
      let rawLength = raw.length;

      let uInt8Array = new Uint8Array(rawLength);

      for (let i = 0; i < rawLength; ++i) {
        uInt8Array[i] = raw.charCodeAt(i);
      }
      return new Blob([uInt8Array], { type: contentType });
    },
    changeLineName(v) {
      if (Object.keys(this.line).length >= 1) {
        for (let key in this.line) {
          this.line[key].setStyle(Q.Styles.LABEL_VISIBLE, v);
        }
      }
    },
    changeLineShow(v) {
      if (!this.tuopuGraph.count) return;
      // 线别显示隐藏 而不是重新请求接口
      // if (v) {
      //   this.commonParams.getFiber(this.neManageIds).then((res) => {
      //     this.createLine(res.data);
      //   });
      // } else {
      //   for (let key in this.line) {
      //     this.tuopuGraph.removeElement(this.line[key]);
      //     delete this.line[key];
      //   }
      // }
      if (Object.keys(this.line).length) {
        // 优化线别显示隐藏 xx
        for (let key in this.line) {
          this.line[key].visible = !this.line[key].visible;
        }
      } else {
        // 查询没有 但又操作选择勾选纤缆
        if (this.isShowLine) {
          this.commonParams.getFiber(this.neManageIds).then((res) => {
            this.createLine(res.data);
          });
        }
      }
    },
    changeLineProtect(v) {
      if (!this.tuopuGraph.count) return;
      if (v) {
        axios
          .post(BASE_URL + "a/protect/ProtectSubnet/selectProtectSubnetLink", {
            neManageIds: this.protectneManageIds,
          })
          .then((res) => {
            if (res.data.code == 1) {
              //多余线上提示问题修复
              for (let key in this.projectLine) {
                this.tuopuGraph.removeElement(this.projectLine[key]);
                delete this.projectLine[key];
              }
              this.projectMethod(res.data.data);
            } else {
              this.$Message.warning(res.data.msg);
            }
          })
          .catch((error) => {
            //console.log(error);
          });
      } else {
        for (let key in this.projectLine) {
          this.tuopuGraph.removeElement(this.projectLine[key]);
          delete this.projectLine[key];
        }
      }
    },
    changeEyesShow(v) {
      this.overview.setVisible(v);
      this.overview.setGraph(v ? this.tuopuGraph : null);
    },
    projectMethod(params) {
      let color = "";
      for (let k = 0; k < params.length; k++) {
        if (params[k].snkNeId && params[k].srcNeId) {
          for (let key1 in this.graphNode) {
            if (params[k].snkNeId == this.graphNode[key1].deviceId) {
              for (let key2 in this.graphNode) {
                if (params[k].srcNeId == this.graphNode[key2].deviceId) {
                  this.projectLine[params[k].linkId] =
                    this.tuopuGraph.createEdge(
                      params[k].nativeEmsName,
                      this.graphNode[key2],
                      this.graphNode[key1]
                    );
                  if (params[k].mappingDirection == 0) {
                    color = "#ccc";
                  } else if (params[k].mappingDirection == 1) {
                    color = "blue";
                  } else if (params[k].mappingDirection == 2) {
                    color = "pink";
                  } else if (params[k].mappingDirection == 3) {
                    color = "#00E676";
                  }
                  //连线样式
                  this.projectLine[params[k].linkId].setStyle(
                    Q.Styles.EDGE_BUNDLE_TYPE,
                    Q.Consts.EDGE_BUNDLE_TYPE_PARALLEL
                  );
                  //颜色
                  this.projectLine[params[k].linkId].setStyle(
                    Q.Styles.EDGE_COLOR,
                    color
                  );
                  this.projectLine[params[k].linkId].setStyle(
                    Q.Styles.ARROW_TO,
                    false
                  );
                  this.projectLine[params[k].linkId].setStyle(
                    Q.Styles.LABEL_COLOR,
                    "#909090"
                  );
                  // 0-4=1PX
                  // 4-15=2px
                  // 16-63=4px
                  // 大于64=6px
                  if (Number(params[k].vc4Number) < 4) {
                    this.projectLine[params[k].linkId].setStyle(
                      Q.Styles.EDGE_WIDTH,
                      1
                    );
                  } else if (
                    Number(params[k].vc4Number) >= 4 &&
                    Number(params[k].vc4Number) <= 15
                  ) {
                    this.projectLine[params[k].linkId].setStyle(
                      Q.Styles.EDGE_WIDTH,
                      2
                    );
                  } else if (
                    Number(params[k].vc4Number) >= 16 &&
                    Number(params[k].vc4Number) <= 63
                  ) {
                    this.projectLine[params[k].linkId].setStyle(
                      Q.Styles.EDGE_WIDTH,
                      4
                    );
                  } else if (Number(params[k].vc4Number) >= 64) {
                    this.projectLine[params[k].linkId].setStyle(
                      Q.Styles.EDGE_WIDTH,
                      6
                    );
                  }

                  // this.projectLine[params[k].linkId].setStyle(
                  //   Q.Styles.EDGE_WIDTH,
                  //   params[k].layerRate - 72
                  // );
                  //保护类型flag
                  this.projectLine[params[k].linkId].protectFlag =
                    "protectFlag";
                  //添加diffColor属性
                  this.projectLine[params[k].linkId].diffColor =
                    params[k].mappingDirection;
                  //添加layerRate属性
                  this.projectLine[params[k].linkId].layerRate =
                    params[k].layerRate;
                  //添加psnId属性
                  this.projectLine[params[k].linkId].psnId = params[k].psnId;
                }
              }
            }
          }
        }
      }
    },
    addNodeFlag(node) {
      let label = new Q.ImageUI(this.Yset_1);
      label.position = Q.Position.LEFT_TOP;
      label.anchorPosition = Q.Position.LEFT_BOTTOM;
      label.size = { width: 15, height: 15 };
      label.offsetX = 15;
      label.offsetY = 15;
      node.addUI(label);
      return node;
    },
  },
  beforeDestroy() {
    //去除页面切换的文字提示
    let old = this.tuopuGraph.interactionMode;
    this.tuopuGraph.interactionMode = null;
    this.tuopuGraph.interactionMode = old;
  },
};
</script>

<style scoped lang="less">
.tuoPu {
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow-x: auto;
  padding: 0 20px 15px 20px;
  text-align: left;
  min-width: 900px;
  header {
    height: 60px;
    display: flex;
    justify-content: space-between;
    .navClass {
      display: flex;
      align-items: center;
      li {
        margin-left: 20px;
        > .ivu-tooltip {
          margin-top: 5px;
        }
        img {
          display: inline-block;
          width: 20px;
          height: 20px;
        }
      }
      .searchInputclass {
        position: relative;
        .icon_class--name {
          position: absolute;
          top: 5px;
          right: 5px;
          display: inline-block;
          width: 20px;
          height: 20px;
          cursor: pointer;
        }
      }
    }
  }
  section {
    position: relative;
    flex: 1;
    overflow: hidden;
    > #canvas {
      height: 100%;
    }
    #overview {
      position: absolute;
      right: 10px;
      bottom: 10px;
      width: 200px;
      height: 200px;
      box-shadow: 0px 0px 10px 0px rgba(22, 95, 255, 0.9);
      border: 1px solid rgba(1, 203, 231, 1);
    }
    .navBarClass {
      position: absolute;
      left: 20px;
      top: 20px;
      display: flex;
      li {
        display: flex;
        align-items: center;
        margin-right: 20px;
        /deep/ .ivu-checkbox-wrapper {
          font-size: 12px;
          .ivu-checkbox {
            margin-bottom: 2px;
          }
        }
      }
    }
  }
}
</style>
<style lang="less">
.app-dark .tuoPu {
  color: #fff;
  .navClass {
    .searchInputclass {
      .icon_class--name {
        background: url("../../assets/dark/bodao/d-搜索.svg") no-repeat;
      }
      ::-webkit-input-placeholder {
        color: rgba(221, 221, 221, 1);
      }
      input {
        border: 1px solid rgba(1, 184, 251, 1);
        background: rgba(104, 232, 255, 0.08);
      }
    }
  }
  section {
    border: 1px solid rgba(1, 203, 231, 1);
    background: rgba(0, 31, 83, 0.7);
    box-shadow: 0px 0px 15px 0px rgba(22, 95, 255, 0.9) inset;
  }
}
.app-light .tuoPu {
  color: #333;
  .navClass {
    .searchInputclass {
      .icon_class--name {
        background: url("../../assets/light/bodao/b-搜索.svg") no-repeat;
      }
      background: rgba(255, 255, 255, 1);
      ::-webkit-input-placeholder {
        color: rgba(153, 153, 153, 1);
      }
      input {
        background: rgba(224, 234, 255, 0.6);
        border: 1px solid rgba(28, 132, 237, 1);
      }
    }
  }
  section {
    border: 1px solid #43aaff;
    background: rgba(236, 241, 255, 1);
    box-shadow: 0px 2px 4px 0px rgba(170, 225, 243, 0.5);
  }
}

.ivu-select-multiple {
  .ivu-select-item-selected {
    background: rgba(25, 30, 41, 0.4);
  }
  .ivu-select-selection {
    > div {
      display: flex;
    }
  }
}

.dropdown-menu {
  position: absolute;
  top: 100%;
  left: 0;
  z-index: 1000;
  display: none;
  float: left;
  min-width: 132px;
  padding: 5px 0;
  margin: 2px 0 0;
  list-style: none;
  font-size: 14px;
  text-align: center;
  -webkit-background-clip: padding-box;
  background-clip: padding-box;
  background: rgba(9, 53, 121, 1);
}
.dropdown-menu.pull-right {
  right: 0;
  left: auto;
}
.dropdown-menu .divider {
  height: 1px;
  margin: 9px 0;
  overflow: hidden;
  background-color: #e5e5e5;
}
.dropdown-menu > li > a {
  display: block;
  padding: 3px 20px;
  clear: both;
  font-weight: normal;
  line-height: 1.42857143;
  white-space: nowrap;
  color: white;
}
.dropdown-menu > li > a:hover,
.dropdown-menu > li > a:focus {
  text-decoration: none;
}
.dropdown-menu > .active > a,
.dropdown-menu > .active > a:hover,
.dropdown-menu > .active > a:focus {
  color: #ffffff;
  text-decoration: none;
  outline: 0;
}
.dropdown-menu > .disabled > a,
.dropdown-menu > .disabled > a:hover,
.dropdown-menu > .disabled > a:focus {
  color: #777777;
}
.dropdown-menu > .disabled > a:hover,
.dropdown-menu > .disabled > a:focus {
  text-decoration: none;
  background-color: transparent;
  background-image: none;
  filter: progid:DXImageTransform.Microsoft.gradient(enabled = false);
  cursor: not-allowed;
}
//深蓝色
.dark_dropdown_menu {
  border: 1px solid rgba(1, 203, 231, 1);
}
.dark_dropdown_menu > li > a:hover,
.dark_dropdown_menu > li > a:focus {
  background: rgba(53, 143, 195, 1);
}
//蓝色
.light_dropdown_menu {
  background: rgba(67, 170, 255, 1);
  li > a:hover,
  li > a:focus {
    background: rgba(208, 225, 255, 1);
    color: black;
  }
}
</style>

// git 信息
