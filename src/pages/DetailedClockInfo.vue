<template>
  <div class="mt-2">
    <div class="mx-2 text-s">
      <van-cell-group inset>
        <van-field
            v-model="state.initClockInfo.phone"
            readonly
            label="工学云账号："
        />
        <van-field
            v-model="state.initClockInfo.password"
            name="password"
            label="工学云密码："
            readonly
        />
        <van-collapse v-model="doClockInfo">
          <van-collapse-item title="打卡信息:" name="1">
            <van-field
                v-model="state.initClockInfo.nikeName"
                name="nikeName"
                label="姓名："
                readonly
            />
            <van-field
                v-model="state.initClockInfo.jobAddress"
                name="jobAddress"
                label="公司地址："
                readonly
            />
            <van-field
                v-model="state.initClockInfo.clockAddress"
                name="clockAddress"
                label="打卡地址："
                readonly
            />
            <van-field
                v-model="state.initClockInfo.jobName"
                name="weekPosition"
                label="工作职位"
                readonly
            />
            <van-field
                v-model="state.initClockInfo.longitude"
                label="经度："
                readonly
            />
            <van-field
                v-model="state.initClockInfo.latitude"
                name="latitude"
                readonly
                label="纬度："
            />
          </van-collapse-item>
        </van-collapse>
        <van-collapse v-model="report" accordion>
          <van-collapse-item title="报告信息" name="1">
            <van-field
                v-model="state.clockInfo.reportSource"
                name="latitude"
                readonly
                label="报告来源："
            />
            <van-collapse v-model="activeNames" accordion>
              <div v-if="state.generatingReport">
                <van-collapse-item title="日报" v-if="state.clockInfo.startDayLyNewspaper" name="1">
                  <van-field
                      v-model="state.report.dayReport.dayTitle"
                      name="dayTitle"
                      label="标题"
                      readonly
                  />
                  <van-field
                      v-model="state.report.dayReport.dayContent"
                      name="dayContent"
                      type="textarea"
                      autosize
                      rows="2"
                      maxlength="1200"
                      show-word-limit
                      label="内容"
                      readonly
                  />
                </van-collapse-item>
                <van-collapse-item title="周报" v-if="state.clockInfo.startWeekLyNewspaper" name="2">
                  <van-field
                      v-model="state.report.weekReport.weekTitle"
                      name="weekTitle"
                      label="标题"
                      readonly
                  />
                  <van-field
                      v-model="state.report.weekReport.weekContent"
                      name="weekContent"
                      type="textarea"
                      autosize
                      rows="2"
                      maxlength="1200"
                      show-word-limit
                      label="内容"
                      readonly
                  />
                </van-collapse-item>
                <van-collapse-item title="月报" v-if="state.clockInfo.startMonthLyNewspaper" name="3">
                  <van-field
                      v-model="state.report.monthReport.monthTitle"
                      name="monthTitle"
                      label="标题"
                      readonly
                  />
                  <van-field
                      v-model="state.report.monthReport.monthContent"
                      name="monthContent"
                      type="textarea"
                      autosize
                      rows="2"
                      maxlength="1200"
                      show-word-limit
                      label="内容"
                      readonly
                  />
                </van-collapse-item>
              </div>
              <div v-else>
                <div class="h-12 mt-2 justify-center items-center flex">
                  <van-loading color="#0094ff" vertical>
                    <template #icon>
                      <van-icon name="star-o" size="30"/>
                    </template>
                    报告生成中...
                  </van-loading>
                </div>
              </div>
            </van-collapse>
          </van-collapse-item>
        </van-collapse>
        <van-field
            v-model="state.clockInfo.startTime"
            readonly
            name="startTime"
            label="上班打卡："
        />
        <van-field
            v-model="state.clockInfo.endTime"
            readonly
            name="endTime"
            label="下班打卡："
        />
        <van-field name="clockDay" label="打卡周期" readonly>
          <template #input>
            <van-checkbox-group v-model="state.clockInfo.clockDay" disabled @change="groupCheckedA"
                                direction="horizontal">
              <van-checkbox name="2" shape="square">周一</van-checkbox>
              <br>
              <van-checkbox name="3" shape="square">周二</van-checkbox>
              <br>
              <van-checkbox name="4" shape="square">周三</van-checkbox>
              <br>
              <van-checkbox name="5" shape="square">周四</van-checkbox>
              <br>
              <van-checkbox name="6" shape="square">周五</van-checkbox>
              <br>
              <van-checkbox name="7" shape="square">周六</van-checkbox>
              <br>
              <van-checkbox name="1" shape="square">周日</van-checkbox>
            </van-checkbox-group>
          </template>
        </van-field>
        <van-field name="clockDays" label="打卡天数：">
          <template #input>
            <van-stepper disabled v-model="state.clockInfo.clockDays"/>
          </template>
        </van-field>
        <van-collapse v-model="getDevice">
          <van-collapse-item title="设备信息" name="1">
            <van-field
                v-model="state.clockInfo.device"
                name="device"
                label="设备信息："
                readonly
            />
          </van-collapse-item>
        </van-collapse>
        <van-collapse v-model="massagePush">
          <van-collapse-item title="消息推送" name="1">
            <van-field
                v-model="state.clockInfo.email"
                name="email"
                label="邮箱："
                readonly
            />
            <van-field
                v-model="state.clockInfo.pushToken"
                name="pushplusToken"
                label="微信推送Token："
                readonly
            />
          </van-collapse-item>
        </van-collapse>
      </van-cell-group>
    </div>
  </div>
  <br>
  <div class="mx-2 text-s">
    <van-cell-group inset>
      <div class="flex justify-center items-center space-x-5 h-16">
        <div @click="doClock(state.clockInfo)">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
               stroke="currentColor" class="w-8 h-8">
            <path stroke-linecap="round" stroke-linejoin="round"
                  d="M15.042 21.672L13.684 16.6m0 0l-2.51 2.225.569-9.47 5.227 7.917-3.286-.672zm-7.518-.267A8.25 8.25 0 1120.25 10.5M8.288 14.212A5.25 5.25 0 1117.25 10.5"/>
          </svg>
        </div>
        <div @click="openClock(state.clockInfo)"
             v-if="state.clockInfo.clockStatus!==1&&state.clockInfo.clockStatus!==2">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
               stroke="currentColor" class="w-8 h-8">
            <path stroke-linecap="round" stroke-linejoin="round"
                  d="M5.25 5.653c0-.856.917-1.398 1.667-.986l11.54 6.348a1.125 1.125 0 010 1.971l-11.54 6.347a1.125 1.125 0 01-1.667-.985V5.653z"/>
          </svg>
        </div>
        <div @click="stopClock(state.clockInfo)"
             v-if="state.clockInfo.clockStatus===1||state.clockInfo.clockStatus===2">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
               stroke="currentColor" class="w-8 h-8">
            <path stroke-linecap="round" stroke-linejoin="round"
                  d="M14.25 9v6m-4.5 0V9M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
          </svg>
        </div>
        <div @click="historicalRecord(state.clockInfo)">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
               stroke="currentColor" class="w-8 h-8">
            <path stroke-linecap="round" stroke-linejoin="round"
                  d="M12 6v6h4.5m4.5 0a9 9 0 11-18 0 9 9 0 0118 0z"/>
          </svg>
        </div>
        <div @click="updateClock(state.clockInfo.id)">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
               stroke="currentColor" class="w-8 h-8">
            <path stroke-linecap="round" stroke-linejoin="round"
                  d="M16.862 4.487l1.687-1.688a1.875 1.875 0 112.652 2.652L10.582 16.07a4.5 4.5 0 01-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 011.13-1.897l8.932-8.931zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0115.75 21H5.25A2.25 2.25 0 013 18.75V8.25A2.25 2.25 0 015.25 6H10"/>
          </svg>
        </div>
        <div @click="deleteClock(state.clockInfo)">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
               stroke="currentColor" class="w-8 h-8">
            <path stroke-linecap="round" stroke-linejoin="round"
                  d="M14.74 9l-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 01-2.244 2.077H8.084a2.25 2.25 0 01-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 00-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 013.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 00-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 00-7.5 0"/>
          </svg>
        </div>
      </div>
    </van-cell-group>
  </div>
  <br/>
</template>

<script setup lang="ts">
import {useRoute, useRouter} from "vue-router";
import {onMounted, reactive, ref} from "vue";
import {showConfirmDialog, showFailToast, showSuccessToast, showToast} from "vant";
import {ClockInControllerService, ClockInInfoControllerService} from "../services/moguding-backend";
import {useBackClockInfoStore} from "../stores/backupClockInfo";

const backClockInfoStore = useBackClockInfoStore();
const getDevice = ref([]);
const massagePush = ref([]);
const doClockInfo = ref([]);
const report = ref([]);
const doClock = async (item) => {
  item.clockStatus = 1
  const res = await ClockInControllerService.startingClockInUsingPost({id: item.id})
  if (res.data && res.code === 0) {
    showSuccessToast("打卡开启成功")
  }
}
const openClock = async (item) => {
  item.clockStatus = 1
  const res = await ClockInControllerService.startingClockInUsingPost({id: item.id})
  if (res.data && res.code === 0) {
    showSuccessToast("打卡开启成功")
  }
}
const stopClock = async (item) => {
  item.clockStatus = 0
  const res = await ClockInControllerService.stopClockInUsingPost({id: item.id})
  if (res.data && res.code === 0) {
    showSuccessToast("打卡暂停成功")
  }
}
const historicalRecord = (item) => {
  router.push({path: '/historicalRecord', query: {phone:item.phone}})
}
const deleteClock = async (item) => {
  showConfirmDialog({
    title: '确认删除该打卡信息？',
    message:
        '请确认是否删除账号：' + item.phone,
  })
      .then(async () => {
        const res = await ClockInInfoControllerService.deleteClockInInfoUsingPost({id: item.id})
        if (res.data && res.code === 0) {
          showSuccessToast("删除成功")
          backClockInfoStore.clockList = backClockInfoStore.clockList.filter(clock => clock.id !== item.id)
          backClockInfoStore.backClockInfo = backClockInfoStore.backClockInfo.filter(clock => clock.id !== item.id)
        }
      })
      .catch(() => {
        // on cancel
      });
}
const updateClock = (id) => {
  router.push({
    path: '/update',
    query: {id: id}
  })
}

const router = useRouter();
const route = useRoute();
const state = reactive({
  result: '',
  activeIndex: null,
  generatingReport: true,
  defaultStartTime: ['08', '00', '00'],
  defaultEndTime: ['18', '00', '00'],
  loading: false,
  report: {
    dayReport: {
      type: "day",
      dayTitle: "",
      dayContent: ''
    },
    weekReport: {
      type: "week",
      weekTitle: "",
      weekContent: ''
    },
    monthReport: {
      monthTitle: "",
      type: "month",
      monthContent: ''
    },
  },
  getClockInfoStatus: false,
  initClockInfo: {
    phone: "qimuuuu",
    jobName: '室内设计',
    password: "12345678",
    latitude: '123.45',
    longitude: '123.45',
    jobAddress: "河南省郑州市四六区嵩山路政通路亿辰山海间3号楼1单元1309",
    clockAddress: "河南省 · 郑州市 · 四六区 · 在山海间附近"
  },
  clockInfo: {
    pushPushToken: '22sdf222222222222eas',
    email: '3444444@qq.com',
    device: '',
    clockDays: 23,
    reportSource: 'AI定制化报告',
    startDayLyNewspaper: true,
    startWeekLyNewspaper: true,
    startMonthLyNewspaper: false,
    startTime: '08:07:00',
    endTime: '18:45:00',
    clockDay: ['0', "4", "5", "6"],
  }
});
const activeNames = ref(['1']);
onMounted(async () => {
  const {id} = route.params
  if (!id) {
    showFailToast("打卡信息不存在")
    return
  }
  const res = await ClockInInfoControllerService.getClockInInfoByIdUsingGet(id)
  if (res.data && res.code === 0) {
    state.initClockInfo = res.data
    state.clockInfo = res.data
    state.clockInfo.clockDay = JSON.parse(res.data.selectClockDay)
    state.clockInfo.startDayLyNewspaper = res.data.dailyNewspaperStatus !== 0
    state.clockInfo.startWeekLyNewspaper = res.data.weekNewspaperStatus !== 0
    state.clockInfo.startMonthLyNewspaper = res.data.monthNewspaperStatus !== 0
    if (res.data.reportSource === 1) {
      state.clockInfo.reportSource = "AI定制化报告"
    }
    if (res.data.reportSource === 0) {
      state.clockInfo.reportSource = "未开启"
    }
    if (res.data.reportSource === 2) {
      state.clockInfo.reportSource = "报告库"
    }
    if (res.data.reportSource === 3) {
      state.clockInfo.reportSource = "自己填写"
    }
    state.generatingReport = res.data.aiReportStatus === 2 || res.data.aiReportStatus === 0
    state.report = res.data.report

  } else {
    showFailToast("打卡信息不存在")
   await router.push('/clockInfo?clockType=gxy&tagType=all')
  }
})

</script>

<style scoped></style>
