<template>
  <Card shadow @mouseover.native="isDisplayDate=false" @mouseout.native="isDisplayDate=true">
    <p slot="title" style="line-height:16px">
      <Row>
        <Col span="20">
        <p style="text-overflow:ellipsis;white-space:nowrap" :title="notice.sender.file">
          {{notice.sender.file}}
        </p>
        </Col>
        <Col span="4" align="right">
          <div v-if="isDisplayDate">
            <p style="font-weight:400;font-size:12px">
              {{timestampToTime(notice.timestamp)}}
            </p>
          </div>
          <div v-else>
            <a><Icon type="md-close" @click="deleteNotice(notice.id)"/></a>
          </div>
        </Col>
      </Row>
    </p>
    <p style="word-break:break-all">{{notice.message}}</p>
    <p>
      <b><a href="#" @click="jump(notice)">Create new issue</a></b>
    </p>
  </Card>
</template>

<script>
export default {
  name: "noticeMessage",
  props: ["notice"],
  data() {
    return {
      isDisplayDate: true,
      jumpToUrl: null,
      jumpToName: null
    };
  },
  methods: {
    timestampToTime(timeStamp){
      let date = new Date(timeStamp * 1000)
      let hour = date.getHours() + ':'
      let minute = (date.getMinutes() < 10 ? '0'+date.getMinutes() : date.getMinutes()) + ':'
      let second = (date.getSeconds() < 10 ? '0'+date.getSeconds() : date.getSeconds())
      return hour + minute + second
    },
    deleteNotice(noticeId){
      this.$store.dispatch('deleteNotice', noticeId)
    },
    jump(notice) {
      // TODO: support select manifest
      // store.state.manifest[0]: only one manifest are supported in v1.0
      for(const menuItem of this.$store.state.menu){
        if (menuItem['params'] && this.$store.state.manifest[0] === menuItem['params']['name']){
          this.$store.commit('setActiveName', menuItem.title)
          this.jumpToUrl = menuItem.params.src
          this.jumpToName = menuItem.params.name
          break
        }
      }
      this.$store.commit('plugin/setSrc', this.jumpToUrl)
      this.$router.push({name:'plugin-view', params:{name:this.jumpToName}})
      this.$store.dispatch("createIssue", notice)
      this.$store.dispatch('deleteNotice', notice.id)
      
    }
  }
}
</script>

<style>
.ivu-card > .ivu-card-head{
  padding: 5px 5px 3px 5px;
  font-size: 12px;
}
.ivu-card > .ivu-card-body{
  padding: 5px 5px 5px 8px;
  font-size: 12px;
}
</style> 