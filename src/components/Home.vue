<template>
  <div class="card-body">
    <h1 class="mb-4">Scenario maker</h1>

    <form action="#">
      <h5>Général</h5>
      <Input name="Name" v-model="scenario.name" />
      <hr />
      <h5>Oos</h5>
      <div class="form-group" v-for="(sentenceOo, index) in scenarioOos" :key="index">
        <select class="form-control" v-model="sentenceOo.ooUuid">
          <option v-for="oo in oos" :key="oo.id" :value="oo.uuid">{{ oo.name }}</option>
        </select>
      </div>
      <button class="btn btn-success" @click.prevent="addOo">Ajouter Oo</button>
      <hr />
      <h5>Phrases annexes</h5>
      <div class="form-group">
        <div class="card mb-2" v-for="(scenarioAnnexe, index) in scenarioAnnexes" :key="index">
          <div class="card-header d-flex align-items-center">
            {{ scenarioAnnexe.name }}
            <i
              class="ml-auto fas fa-eye"
              v-if="scenarioAnnexe.visible"
              @click.prevent="scenarioAnnexe.visible = !scenarioAnnexe.visible"
            ></i>
            <i
              class="ml-auto fas fa-eye-slash"
              v-if="!scenarioAnnexe.visible"
              @click.prevent="scenarioAnnexe.visible = !scenarioAnnexe.visible"
            ></i>
          </div>
          <div :class="`card-body ${scenarioAnnexe.visible ? 'd-block' : 'd-none'}`">
            <div class="row">
              <div class="col-6">
                <Input name="Name" v-model="scenarioAnnexe.name" />
              </div>
              <div class="col-6">
                <Input name="Path" v-model="scenarioAnnexe.path" />
              </div>
            </div>
            <label>Type</label>
            <select class="form-control mb-3" v-model="scenarioAnnexe.type">
              <option value="entry:positive">Introduction positive</option>
              <option value="entry:negative">Introduction négative</option>
              <option value="entry">Introduction neutre</option>
              <option value="exit">Phrase de fin</option>
            </select>
            <div class="row">
              <div class="col-6">
                <label>Oo</label>
                <select class="form-control" v-model="scenarioAnnexe.ooUuid">
                  <option v-for="oo in oos" :key="oo.uuid" :value="oo.uuid">{{ oo.name }}</option>
                </select>
              </div>
              <div class="col-6 d-flex align-items-center">
                <div class="form-check mt-4">
                  <label class="form-check-label">
                    <input
                      class="form-check-input"
                      type="checkbox"
                      v-model="scenarioAnnexe.interaction"
                    />
                    Interaction
                  </label>
                </div>
              </div>
            </div>
            <div class="container mt-3" v-if="scenarioAnnexe.interaction">
              <div class="row flex-column">
                <h6>Phrases de sortie</h6>
                <div class="card mb-2" v-for="(s, i) in scenarioAnnexe.sentences" :key="i">
                  <div class="card-header d-flex align-items-center">
                    {{ s.name }}
                    <i
                      class="ml-auto fas fa-eye"
                      v-if="s.visible"
                      @click.prevent="s.visible = !s.visible"
                    ></i>
                    <i
                      class="ml-auto fas fa-eye-slash"
                      v-if="!s.visible"
                      @click.prevent="s.visible = !s.visible"
                    ></i>
                  </div>
                  <div :class="`card-body ${s.visible ? 'd-block' : 'd-none'}`">
                    <div class="row">
                      <div class="col-6">
                        <Input name="Name" v-model="s.name" />
                      </div>
                      <div class="col-6">
                        <Input name="Path" v-model="s.path" />
                      </div>
                    </div>
                    <label>Oo</label>
                    <select class="form-control" v-model="s.ooUuid">
                      <option v-for="oo in oos" :key="oo.uuid" :value="oo.uuid">{{ oo.name }}</option>
                    </select>
                  </div>
                </div>
                <div class="form-grou mb-0">
                  <button
                    class="btn btn-success mt-2"
                    @click.prevent="addSubSentence(scenarioAnnexe)"
                  >Ajouter phrase de sortie</button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <button class="btn btn-success" @click.prevent="addAnnexe">Ajouter phrase</button>
      <hr />
      <h5>Phrases du scénario</h5>
      <div class="form-group">
        <draggable v-model="scenarioSentences" @end="onDragEnd">
          <div class="card mb-2" v-for="(sentence, index) in scenarioSentences" :key="index">
            <div class="card-header d-flex align-items-center">
              {{ sentence.name }}
              <i
                class="ml-auto fas fa-eye"
                v-if="sentence.visible"
                @click.prevent="sentence.visible = !sentence.visible"
              ></i>
              <i
                class="ml-auto fas fa-eye-slash"
                v-if="!sentence.visible"
                @click.prevent="sentence.visible = !sentence.visible"
              ></i>
            </div>
            <div :class="`card-body ${sentence.visible ? 'd-block' : 'd-none'}`">
              <div class="row">
                <div class="col-6">
                  <Input name="Name" v-model="sentence.name" />
                </div>
                <div class="col-6">
                  <Input name="Path" v-model="sentence.path" />
                </div>
              </div>
              <div class="row">
                <div class="col-6">
                  <label>Oo</label>
                  <select class="form-control" v-model="sentence.ooUuid">
                    <option v-for="oo in oos" :key="oo.uuid" :value="oo.uuid">{{ oo.name }}</option>
                  </select>
                </div>
                <div class="col-6 d-flex align-items-center">
                  <div class="form-check mt-4">
                    <label class="form-check-label">
                      <input
                        class="form-check-input"
                        type="checkbox"
                        v-model="sentence.interaction"
                      />
                      Interaction
                    </label>
                  </div>
                </div>
              </div>
              <div class="container mt-3" v-if="sentence.interaction">
                <div class="row flex-column">
                  <h6>Phrases de sortie</h6>
                  <div class="card mb-2" v-for="(s, i) in sentence.sentences" :key="i">
                    <div class="card-header d-flex align-items-center">
                      {{ s.name }}
                      <i
                        class="ml-auto fas fa-eye"
                        v-if="s.visible"
                        @click.prevent="s.visible = !s.visible"
                      ></i>
                      <i
                        class="ml-auto fas fa-eye-slash"
                        v-if="!s.visible"
                        @click.prevent="s.visible = !s.visible"
                      ></i>
                    </div>
                    <div :class="`card-body ${s.visible ? 'd-block' : 'd-none'}`">
                      <div class="row">
                        <div class="col-6">
                          <Input name="Name" v-model="s.name" />
                        </div>
                        <div class="col-6">
                          <Input name="Path" v-model="s.path" />
                        </div>
                      </div>
                      <label>Oo</label>
                      <select class="form-control" v-model="s.ooUuid">
                        <option v-for="oo in oos" :key="oo.uuid" :value="oo.uuid">{{ oo.name }}</option>
                      </select>
                    </div>
                  </div>
                  <div class="form-grou mb-0">
                    <button
                      class="btn btn-success mt-2"
                      @click.prevent="addSubSentence(sentence)"
                    >Ajouter phrase de sortie</button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </draggable>
      </div>
      <button class="btn btn-success" @click.prevent="addSentence">Ajouter phrase</button>
      <hr />
      <div class="form-group">
        <label for="json">JSON</label>
        <textarea class="form-control" name="json" id="json" v-model="json"></textarea>
      </div>
    </form>
  </div>
</template>

<script>
import { v4 as uuidv4 } from "uuid";
import Input from "./Input";
import draggable from "vuedraggable";

export default {
  name: "Home",
  components: { Input, draggable },
  data: () => {
    return {
      scenario: {
        uuid: uuidv4(),
        name: ""
      },
      scenarioOos: [],
      scenarioAnnexes: [],
      scenarioAudios: [],
      scenarioSentences: [],
      oos: [
        {
          uuid: "1c115903-1aa5-4216-b438-5e455124f66d",
          name: "Disc'Oo"
        },
        {
          uuid: "23c7e653-f1a6-435c-9f58-24f9efb8c5e1",
          name: "Cin'Oo'che"
        },
        {
          uuid: "4d8cf885-f681-452f-97a6-5077744eb4be",
          name: "Inf'Oo"
        },
        {
          uuid: "a10058d0-46e0-4e54-bf54-72bef1ae283f",
          name: "Y'Oo'ga"
        },
        {
          uuid: "ca5f71b5-8dc0-4acd-9d58-3c3035622afe",
          name: "Végét'Oo"
        },
        {
          uuid: "92021a2e-77d3-48b6-bd31-3e86d6792e05",
          name: "Wh'Oo'w"
        },
        {
          uuid: "55a7bed3-a9a5-4c6c-9913-d8966d8dfc59",
          name: "C'Oo'mique"
        },
        {
          uuid: "1fd0f001-5950-4f22-983d-11d08cee3add",
          name: "Méli-mél'Oo"
        }
      ]
    };
  },
  computed: {
    json: {
      get: function() {
        const json = {
          scenario: null,
          // sentences: [],
          oos: [],
          audios: []
        };

        json.scenario = this.scenario;
        json.oos = this.scenarioOos;

        this.scenarioAnnexes.forEach(annexe => {
          const uuid = uuidv4();

          json.audios.push({
            uuid: uuid,
            name: annexe.name,
            url: annexe.path,
            type: annexe.type,
            interaction: annexe.interaction,
            audibleUuid: this.scenario.uuid,
            audibleType: "scenario",
            ooUuid: annexe.ooUuid
          });

          if (annexe.sentences.length > 0) {
            annexe.sentences.forEach(s => {
              json.audios.push({
                uuid: uuidv4(),
                name: s.name,
                url: s.path,
                type: "dislike",
                audibleUuid: uuid,
                audibleType: "audio",
                ooUuid: s.ooUuid
              });
            });
          }
        });

        this.scenarioSentences.forEach(sentence => {
          const uuid = uuidv4();
          json.audios.push({
            uuid: uuid,
            name: sentence.name,
            url: sentence.path,
            type: sentence.type,
            order: sentence.order,
            interaction: sentence.interaction,
            audibleUuid: this.scenario.uuid,
            audibleType: "scenario",
            ooUuid: sentence.ooUuid
          });

          sentence.sentences.forEach(s => {
            json.audios.push({
              uuid: uuidv4(),
              name: s.name,
              url: s.path,
              type: s.type,
              audibleUuid: uuid,
              audibleType: "audio",
              ooUuid: s.ooUuid
            });
          });
        });

        delete json.sentences;

        return JSON.stringify(json, undefined, 2);
      },
      set: function(value) {
        const object = JSON.parse(value);

        this.scenarioOos = object.oos;
        this.scenario = object.scenario;

        this.scenarioSentences = [];
        this.scenarioAnnexes = [];

        object.audios.forEach(sentence => {
          if (sentence.audibleType === "scenario" && sentence.type === null) {
            this.scenarioSentences.push({
              uuid: sentence.uuid,
              name: sentence.name,
              path: sentence.url,
              interaction: sentence.interaction,
              type: sentence.type,
              ooUuid: sentence.ooUuid,
              order: sentence.order,
              visible: false,
              sentences: []
            });
          }
        });

        object.audios.forEach(audio => {
          if (audio.audibleType === "scenario") {
            if (audio.type !== null) {
              this.scenarioAnnexes.push({
                uuid: audio.uuid,
                name: audio.name,
                path: audio.url,
                interaction: audio.interaction,
                type: audio.type,
                ooUuid: audio.ooUuid,
                visible: false,
                sentences: []
              });
            }
          } else if (audio.audibleType === "audio") {
            if (audio.type !== null) {
              let sentence = this.scenarioSentences.find(
                s => s.uuid === audio.audibleUuid
              );

              if (!sentence) {
                sentence = this.scenarioAnnexes.find(
                  s => s.uuid === audio.audibleUuid
                );
              }

              sentence.sentences.push({
                uuid: audio.uuid,
                name: audio.name,
                path: audio.url,
                type: audio.type,
                ooUuid: audio.ooUuid,
                visible: false
              });
            }
          }
        });
      }
    }
  },
  methods: {
    onDragEnd() {
      this.scenarioSentences.forEach((sentence, index) => {
        sentence.order = index;
      });
    },
    addOo() {
      this.scenarioOos.push({
        scenarioUuid: this.scenario.uuid,
        ooUuid: null
      });
    },
    addAnnexe() {
      this.scenarioAnnexes.push({
        uuid: uuidv4(),
        name: null,
        path: null,
        type: null,
        interaction: false,
        order: this.scenarioSentences.length,
        ooUuid: null,
        visible: true,
        sentences: []
      });
    },
    addSentence() {
      this.scenarioSentences.push({
        uuid: uuidv4(),
        name: null,
        path: null,
        interaction: false,
        type: null,
        ooUuid: null,
        order: this.scenarioSentences.length,
        visible: true,
        sentences: []
      });
    },
    addSubSentence(sentence) {
      sentence.sentences.push({
        uuid: uuidv4(),
        name: null,
        path: null,
        interaction: false,
        type: "dislike",
        ooUuid: null,
        order: sentence.sentences.length,
        visible: true
      });
    }
  }
};
</script>

<style lang="scss" scoped>
#json {
  height: 600px;
}
</style>
