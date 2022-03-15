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
          "callsign": "Daedalus",
          "alias": 'Falkner Daedo',
          "code": "d8754162-839d-4fc2-b4ad-5466312aae96//40e03174-6acd-48e5-befa-57dd759f6381",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Old Dawg"
        },
        {
          "callsign": "Null",
          "alias": 'Hans Paiz',
          "code": "8e46504c-fedc-458f-aacb-6703aec0d4c2//5c0f5347-a01a-4ea1-9dd6-c85e7b1da357",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Humans Need Not Apply"
        },
        {
          "callsign": "Null",
          "alias": 'Myriel Patete',
          "code": "89fed27a-842a-4b43-924e-44ab686a6bf2//ea874d99-890e-419b-a572-a4b661a299da",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Unwelcome Advice"
        },
        {
          "callsign": "Null",
          "alias": 'Nicolae Mitrea',
          "code": "edc0caa7-0431-426d-8c91-dd2b0e622685//da17f4ca-65f6-4866-84ac-75b12eccddff",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Grim Herculean Task"
        },
        {
          "callsign": "Cursed",
          "alias": 'Tazlo the 13056th',
          "code": "da25211a-24ba-44fe-87fe-52508d850b87//3fd4da31-7a63-44d8-a895-4d9df50aa2bb",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Trick or Treat"
        },
        {
          "callsign": "Red",
          "alias": 'ERR_NO_DATA',
          "code": "6f33360c-d670-476f-bf55-96385cb26c1e//7f176550-6518-4388-bb46-6e404ea10b35",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "A Thousand Lives"
        },
        {
          "callsign": "Null",
          "alias": 'ERR_NO_DATA',
          "code": "ERR_OMNINET_CONNECTION_NOT_FOUND_..._ATTEMPTING_REBOOT_..._REBOOT_UNSUCCESSFUL",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "ERR_NO_DATA"
        },
      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u",
        "system": "Ardennes-3",
        "gate": "Atlas-Quanokrim",
        "ring": "Atlas-Line",
        "headerTitle": "Union",
        "headerSubtitle": "Naval Department",
        "subheaderTitle": "Auxiliary Team",
        "subheaderSubtitle": "Charlie-Foxtrot",
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
