<template>
  <div>
    <v-card class="pa-5">
      <h3>Opções do Host</h3>
      <br>
      <v-form>
        <v-slider
          label="Distância da Voz"
          min="2.5"
          max="10"
          step="0.1"
          v-bind="attrs"
          v-on="on"
          v-model="$store.state.options.falloff"
          :readonly="$store.state.host !== $store.state.me.uuid"
          :disabled="$store.state.options.falloffVision || !$store.state.joinedRoom"
          @change="updateOptions"
        >
          <template v-slot:append>
            <v-text-field
              v-model="$store.state.options.falloff"
              class="mt-0 pt-0"
              type="number"
              style="width: 60px"
              min="2.5"
              max="10"
              step="0.1"
              :readonly="$store.state.host !== $store.state.me.uuid"
              :disabled="$store.state.options.falloffVision || !$store.state.joinedRoom"
              @change="updateOptions"
            ></v-text-field>
          </template>
        </v-slider>
        <v-checkbox
          label="Ouvir pessoas apenas na sua visão"
          v-model="$store.state.options.falloffVision"
          :readonly="$store.state.host !== $store.state.me.uuid"
          :disabled="!$store.state.joinedRoom"
          @change="updateOptions"
        ></v-checkbox>
        <!--<v-checkbox
          label="Paredes bloqueam a voz"
          v-model="$store.state.options.colliders"
          :readonly="$store.state.host !== $store.state.me.uuid"
          :disabled="!$store.state.joinedRoom"
          @change="updateOptions"
        ></v-checkbox>-->
        <v-checkbox
          label="Ouvir pessoas nas câmeras"
          v-model="$store.state.options.paSystems"
          :readonly="$store.state.host !== $store.state.me.uuid"
          :disabled="!$store.state.joinedRoom"
          @change="updateOptions"
        ></v-checkbox>
      </v-form>
    </v-card>
    <br>
    <v-card class="pa-5">
      <h3>Opções Locais</h3>
      <v-form>
        <v-checkbox
          label="Ouvir todos como fantasma"
          v-model="$store.state.clientOptions.omniscientGhosts"
          :disabled="!$store.state.joinedRoom"
        ></v-checkbox>
      </v-form>
    </v-card>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import { Socket } from 'vue-socket.io-extended'
import { ClientSocketEvents } from '@/models/ClientSocketEvents'
import { HostOptions } from '@/models/RoomModel'

@Component({})
export default class ClientOptions extends Vue {
  updateOptions () {
    this.$socket.client.emit(
      ClientSocketEvents.SetOptions,
      { options: this.$store.state.options }
    )
  }

  @Socket(ClientSocketEvents.SetOptions)
  onSetOptions (payload: { options: HostOptions }) {
    this.$store.state.options = payload.options
  }
}
</script>
<style scoped lang="stylus"></style>
