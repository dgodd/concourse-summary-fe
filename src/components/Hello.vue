<template>
  <div class="hello">
    <router-link v-for="job in relevant" :key="key(job)" :to="job.Job" target="_blank" :class="classes(job)">
      <div class="status">
        <div :class="job.Status" style="width: 100%;"></div>
      </div>

      <div class="inner">
        <span><span style="font-size: 87.2727%;">{{job.Pipeline}}</span></span>
        <span><span style="font-size: 61.1765%;">{{job.Job}}</span></span>
        <span><span style="font-size: 93.8346%;">
          ({{ago(job)}})
          {{runtime(job)}}
        </span></span>
      </div>
    </router-link>
  </div>
</template>

<script>
import moment from 'moment'

export default {
  name: 'hello',
  data () {
    return {
      jobs: []
    }
  },
  created () {
    this.loadJobs()
  },
  computed: {
    relevant () {
      return this.jobs.filter(j => j.Status !== 'succeeded')
    }
  },
  methods: {
    loadJobs () {
      fetch('/jobs.json')
        .then(resp => resp.json())
        .then(jobs => { this.jobs = jobs; console.log(jobs) })
    },
    classes (job) {
      console.log(job.Status)
      if (job.Status === 'started') {
        return 'outer running'
      }
      return 'outer'
    },
    key (job) {
      return `${job.Pipeline}/${job.Job}`
    },
    runtime (job) {
      if (job.EndTime) {
        return moment(job.EndTime).from(job.StartTime, true)
      }
      return moment(job.StartTime).toNow(true)
    },
    ago (job) {
      return moment(job.StartTime).toNow(true)
    }
  }
}
</script>

<style scoped>
.hello {
display: flex;
flex-flow: row wrap;
justify-content: space-around;
}
.outer {display:block;width:300px;height:200px;color:white;background:#5C6C7D;position:relative;margin:4px;}
.status {position:absolute;top:0;bottom:0;left:0;right:0;white-space:nowrap;overflow:hidden;text-align:left;}
.paused_job, .aborted, .errored, .failed, .succeeded {display:inline-block;height:100%;margin:0;padding:0;float:left;}
.paused_job {background:#3498DB;}
.aborted {background:#8F4B2D;}
.errored {background:#E67E21;}
.failed {background:#E74C3C;}
.succeeded {background:#2ECC71;}
.paused {position:absolute;top:0;bottom:0;left:0;right:0;box-sizing:border-box;border:14px solid #2682D5;}
.inner {position:absolute;top:0;bottom:0;left:0;right:0;text-align:center;text-decoration:none;white-space:nowrap;overflow:hidden;display:flex;justify-content:center;flex-direction:column;}
.running .inner {height:100%;}
 @-webkit-keyframes pulseBorder {
  from { outline-offset: 0; }
  to { outline-offset: 7px; }
}
.running {
  -webkit-animation-name: pulseBorder;
  -webkit-animation-iteration-count: infinite;
  -webkit-animation-timing-function: ease;
  -webkit-animation-direction: alternate;
  -webkit-animation-duration: 0.5s;
  outline: solid 7px #F2C500;
  outline-offset: 0;
}
</style>
