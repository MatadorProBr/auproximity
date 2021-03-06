<template>
  <div>
    <v-card class="pa-5">
      <v-form v-model="valid" @submit.prevent="joinRoom(name)">
        <v-text-field
          v-model="name"
          label="Nome no Among Us"
          :rules="[rules.required]"
          outlined
        ></v-text-field>
        <v-text-field
          v-model="gameCode"
          label="Código da Sala"
          :rules="[rules.required, rules.counter6]"
          counter="6"
          maxlength="6"
          outlined
        ></v-text-field>
        <v-select
          v-model="backendType"
          :items="items"
          item-text="backendName"
          item-value="backendType"
          :rules="[rules.required]"
          label="Backend do Servidor"
          required
          outlined
        ></v-select>
        <v-text-field
          v-if="backendType === 2 || backendType === 4"
          v-model="ip"
          label="Nome do domínio (exemplo.com) ou o endereço IP do servidor"
          :rules="[rules.required]"
          outlined
        ></v-text-field>
        <v-select
          v-if="backendType === 1"
          v-model="publicLobbyRegion"
          :items="regions"
          item-text="regionName"
          item-value="regionType"
          label="Região pública do Servidor"
          :rules="[rules.publicLobbyRegion]"
          required
          outlined
        ></v-select>
        <v-btn
          :disabled="!valid"
          color="success"
          class="mr-4"
          type="submit"
        >
          Entrar
        </v-btn>
        <v-btn
          :disabled="!valid"
          color="info"
          class="mr-4"
          @click="copyShareSlug"
        >
          Compartilhar URL
        </v-btn>
        <input :value="shareSlug" id="slug-share">
      </v-form>
    </v-card>
    <JoinModal :game-code="gameCode" @joinroom="joinRoom($event)"/>
    <v-snackbar v-model="showSnackbar">
      URL copiada com sucesso!
    </v-snackbar>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import {
  BackendModel,
  BackendType,
  ImpostorBackendModel,
  PublicLobbyBackendModel,
  PublicLobbyRegion
} from '@/models/BackendModel'
import JoinModal from '@/components/JoinModal.vue'

@Component({
  components: { JoinModal }
})
export default class ServerConnector extends Vue {
  valid = false;
  showSnackbar = false;

  name = '';
  gameCode = this.$route.params.gamecode ? this.$route.params.gamecode.slice(0, 6) : '';

  // Backends
  // eslint-disable-next-line @typescript-eslint/ban-ts-ignore
  // @ts-ignore
  backendType: BackendType = BackendType[this.$route.params.backend || 'PublicLobby'] || BackendType.PublicLobby;
  items = [
    {
      backendName: 'Servidores oficiais do Among Us',
      backendType: BackendType.PublicLobby
    },
    {
      backendName: 'Servidor privado (Imposter)',
      backendType: BackendType.Impostor
    },
    {
      backendName: 'Servidor privado (NodePolus)',
      backendType: BackendType.NodePolus
    },
    {
      backendName: 'BepInEx',
      backendType: BackendType.BepInEx
    }
  ];

  // Impostor and NodePolus Backend
  ip = this.$route.params.region || '';

  // Public Lobby Backend
  // eslint-disable-next-line @typescript-eslint/ban-ts-ignore
  // @ts-ignore
  publicLobbyRegion: PublicLobbyRegion = this.$route.params.region || PublicLobbyRegion.NorthAmerica;
  regionNames = {
    [PublicLobbyRegion.NorthAmerica]: 'North America',
    [PublicLobbyRegion.Europe]: 'Europe',
    [PublicLobbyRegion.Asia]: 'Asia'
  };

  regions = [
    {
      regionName: 'North America',
      regionType: PublicLobbyRegion.NorthAmerica
    },
    {
      regionName: 'Europe',
      regionType: PublicLobbyRegion.Europe
    },
    {
      regionName: 'Asia',
      regionType: PublicLobbyRegion.Asia
    }
  ];

  rules = {
    required (value: string) {
      return !!value || 'Obrigatório'
    },
    counter6 (value: string) {
      return value.length === 6 || 'Máximo 6 caractéres'
    },
    publicLobbyRegion (value: PublicLobbyRegion) {
      return Object.values(PublicLobbyRegion).includes(value)
    }
  };

  joinRoom (name: string) {
    this.name = name
    const backendModel: BackendModel = {
      gameCode: this.gameCode.toUpperCase(),
      backendType: this.backendType
    }
    if (this.backendType === BackendType.PublicLobby) {
      (backendModel as PublicLobbyBackendModel).region = this.publicLobbyRegion
    } else if (this.backendType === BackendType.Impostor || this.backendType === BackendType.NodePolus) {
      (backendModel as ImpostorBackendModel).ip = this.ip
    }
    this.$emit('joinroom', {
      name,
      backendModel
    })
  }

  copyShareSlug () {
    const copyText = document.getElementById('slug-share') as HTMLInputElement

    copyText.select()
    copyText.setSelectionRange(0, 99999)

    document.execCommand('copy')
    this.showSnackbar = true
  }

  get shareSlug () {
    if (this.backendType === BackendType.Impostor || this.backendType === BackendType.NodePolus) {
      return location.origin + '/' + BackendType[this.backendType] + '/' + this.ip + '/' + this.gameCode.toUpperCase()
    } else if (this.backendType === BackendType.PublicLobby) {
      return location.origin + '/' + BackendType[this.backendType] + '/' + this.publicLobbyRegion + '/' + this.gameCode.toUpperCase()
    }
  }
}
</script>
<style scoped lang="stylus">
#slug-share {
  position: absolute;
  left: -9999px
}
</style>
