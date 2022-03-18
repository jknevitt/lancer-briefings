<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "001",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "Bug-Hunt",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Remus",
          "alias": "Zev Tallin",
          "code": "515c99bc-a19e-477d-bf14-f79aedc0b2d9//40fbc9e7-1cff-467f-aec6-2d6541cc5cb9",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Dawn and Eclipse"
        },
        {
          "callsign": "Time-of-Death",
          "alias": "Andrea Harrison",
          "code": "e71bc9e8-019c-400d-9e47-d917d3abf17f//ebbdaae1-4e7f-4b23-aac5-cf245bb0eace",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Phenobarbitol"
        },
        {
          "callsign": "Prometheus",
          "alias": "Elias Teller",
          "code": "020597fb-8fb4-4722-9f94-29ab74b83c43//54454372-9ea4-4cc0-a4f6-815832b03e3e",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Quasar"
        },
        {
          "callsign": "Caliban",
          "alias": "Ösel",
          "code": "b15d0ff7-f18c-4f85-92ca-502b27bf8ec5//46053bb8-3e1e-475c-a40d-4f8e1a959187",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Kilmer"
        },
        {
          "callsign": "Reaper",
          "alias": "Emmit Ashcroft",
          "code": "a962811a-0823-45a9-a8c7-e2a531f73dab//e1382a89-d897-4c0e-826e-07a1260fe4c3",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "The Jackhammer"
        },
      ],
      "header": {
        "planet": "Rocosa",
        "year": "5016u",
        "system": "Cerro-Barajas",
        "gate": "Cerro-Barajas",
        "ring": "Sierra-Madre",
        "headerTitle": "Union",
        "headerSubtitle": "DoJ / HR",
        "subheaderTitle": "Special Circumstances",
        "subheaderSubtitle": 'SC-174 "Joy Division"',
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
