<template>
    <div id="burger-table">
        <Message :msg="msg" v-show="msg" />
        <div>
            <div id="burger-table-heading">
                <div class="order-id">#:</div>
                <div>Cliente:</div>
                <div>Pão:</div>
                <div>Carne:</div>
                <div>Opcionais:</div>
                <div>Ações:</div>
            </div>
        </div>
        <div id="burger-table-rows">
            <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
                <div class="order-number">{{burger.id}}</div>
                <div>{{burger.nome}}</div>
                <div>{{burger.pao}}</div>
                <div>{{burger.carne}}</div>
                <div>
                    <ul>
                        <li v-for="(opcional, index) in burger.opcionais" :key="index">{{opcional}}</li>
                    </ul>
                </div>
                <select name="status" class="status" @change="atualizarBurger($event, burger.id)">
                    <option :value="null" selected disabled hidden>Selecione</option>
                    <option v-for="s in status" :key="s.id" :value="s.tipo" :selected="burger.status == s.tipo">{{s.tipo}}</option>
                </select>
                <button class="delete-btn" @click="deletarBurger(burger.id)">Cancelar</button>
            </div>
        </div>
    </div>
</template>

<script>
    import Message from './Message.vue';
    export default{
        name: 'Dashboard',
        data(){
            return{
                burgers: null,
                burger_id: null,
                status: [],
                msg: ""
            }
        },
        methods:{
            async obterPedidos(){
                const requisicao = await fetch("http://localhost:3000/burgers");

                const data = await requisicao.json();

                this.burgers = data;


            },
            async obterStatus(){
                const requisicao = await fetch("http://localhost:3000/status");

                const data = await requisicao.json();

                this.status = data;
            },
            async deletarBurger(id){
                const requisicao = await fetch(`http://localhost:3000/burgers/${id}`, {method: "DELETE"});

                const resposta = await requisicao.json();

                this.enviarMensagem("Pedido removido com sucesso!");

                this.obterPedidos();
            },
            async atualizarBurger(event, id){
                const option = event.target.value;

                const dataJson = JSON.stringify({status: option});

                const requisicao = await fetch(`http://localhost:3000/burgers/${id}`,
                {
                    method: "PATCH",
                    headers:{"Content-Type": "application/json"},
                    body:dataJson
                });

                this.enviarMensagem(`O pedido N° ${id} foi atualizado para: ${option}`);

                const resposta = await requisicao.json();

                console.log(resposta);
            },
            enviarMensagem(msg){
                this.msg = msg;
                this.LimparMensagem();
            },
            LimparMensagem(){
                setTimeout(() => this.msg = "", 3000);
            }
        },
        components:{Message},
        mounted(){
            this.obterPedidos();
            this.obterStatus();
        }
    }
</script>

<style scoped>
    #burger-table {
      max-width: 1200px;
      margin: 0 auto;
    }
    #burger-table-heading,
    #burger-table-rows,
    .burger-table-row {
      display: flex;
      flex-wrap: wrap;
    }
    #burger-table-heading {
      font-weight: bold;
      padding: 12px;
      border-bottom: 3px solid #333;
    }
    .burger-table-row {
      width: 100%;
      padding: 12px;
      border-bottom: 1px solid #CCC;
    }
    #burger-table-heading div,
    .burger-table-row div {
      width: 19%;
    }
    #burger-table-heading .order-id,
    .burger-table-row .order-number {
      width: 5%;
    }
    select {
      padding: 12px 6px;
      margin-right: 12px;
    }
    .delete-btn {
      background-color: #222;
      color:#fcba03;
      font-weight: bold;
      border: 2px solid #222;
      padding: 10px;
      font-size: 16px;
      margin: 0 auto;
      cursor: pointer;
      transition: .5s;
    }
    
    .delete-btn:hover {
      background-color: transparent;
      color: #222;
    }
    
  </style>