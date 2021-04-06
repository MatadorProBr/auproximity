<template>
 <v-card>
   <div class="text-center py-5">
     <h2>Tutorial</h2>
   </div>
   <div class="pa-4 pb-10">
     <ul>
       <li v-for="(item, i) in items" :key="i">{{ item }}</li>
     </ul>
   </div>
 </v-card>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'

@Component({})
export default class Tutorial extends Vue {
  items = [
    'Digite seu nome e código da partida exatamente como aparece no Among Us.',
    'Escolha o backend. Selecione o "Servidores oficiais do Among Us".',
    'Escolha a região em que você está jogando.',
    'Pressione o botão "Entrar" e compartilhe a URL com seus amigos para convidá-los.',
    'Lembre-se de permitir o microfone e fazer com que todos se juntem à proximidade antes do jogo começar.',
    'Se não funcionar para você na primeira tentativa, atualize a página ou tente um navegador diferente, como Firefox ou Chrome.',
    'Se você ainda estiver em dúvida de como usar o chat de proximidade por voz, veja esse vídeo: https://youtu.be/lh2RHEJ3o64'
  ]
}
</script>
<style scoped lang="stylus">
.highlight
  font-weight bold
  font-size large
</style>
