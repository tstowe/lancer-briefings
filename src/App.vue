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
      "mission_slug": "005",
      "current_md": "",
      "events": "001",
      "missions": [
        {
          "slug": "005",
          "name": "Gone Fishin'",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Clutch",
          "alias": "Elliot Samuel Pack",
          "code": "3d176a31-cdcf-4ec3-812c-b9b6326488c3//ef6299f9-d936-4686-b721-44e897f07421",
          "corpro": "IPS-N",
          "frame": "Lancaster",
          "mech": "Junkyard Dog"
        },
        {
          "callsign": "Ringer",
          "alias": "Adrian Köhler",
          "code": "6115a47b-ade1-4127-ae4f-0f8eeb76ef29//b85e1994-e6ca-47e5-85f8-7b24ad386243",
          "corpro": "IPS-N",
          "frame": "Zheng",
          "mech": "The Tenth Lesson"
        },
        {
          "callsign": "Sanction",
          "alias": "Malleux Despore",
          "code": "ae02e887-ed6f-48fa-94f0-c92b879cddc9//1e4a837e-fd65-45dc-8d48-372d95dfd6bd",
          "corpro": "HA",
          "frame": "Sherman",
          "mech": "Trinitite Catalyst"
        },
        {
          "callsign": "Triple Threat",
          "alias": "Gildayn Reul",
          "code": "00914041-65fe-4fbe-866e-5be01c60c819//7a7e08b2-02fe-4f51-8f8b-da0909e19138",
          "corpro": "SSC",
          "frame": "Black Witch",
          "mech": "Y0R1CK"
        },
      ],
      "header": {
        "planet": "Ras Shamra",
        "year": "5016u",
        "system": "Ptah",
        "gate": "Capitol",
        "ring": "Rocky Mountain-Line",
        "headerTitle": "HA",
        "headerSubtitle": "Harrison Armory",
        "subheaderTitle": "Rendezvous",
        "subheaderSubtitle": "RDI Remote Campus",
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
