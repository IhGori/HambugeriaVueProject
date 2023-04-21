<template>
    <div v-if="pedidos" class="burger-table">
        <div>
            <MessageBurger :mensagem="msg" v-show="mensagemBool" />
        </div>
        <div>
            <table>
                <caption>PEDIDOS</caption>
                <thead>
                    <tr>
                        <th>#:</th>
                        <th>Cliente:</th>
                        <th>Pão:</th>
                        <th>Carne:</th>
                        <th>Opcionais:</th>
                        <th>Ações:</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="pedido in pedidos" :key="pedido.id">
                        <td>{{ pedido.id }}</td>
                        <td>{{ pedido.nome }}</td>
                        <td>{{ pedido.pao }}</td>
                        <td>{{ pedido.carne }}</td>
                        <td>
                            <ul v-for="(opcao, index) in pedido.opcionais" :key="index">
                                <li>{{ opcao }}</li>
                            </ul>
                        </td>
                        <td>
                            <select @change="updateBurger($event,pedido.id)" name="" id="">
                                <option v-for="s in status" :key="s.id" :value="s.tipo" :selected="s.status" >{{ s.tipo }}</option>
                            </select>                            
                            <button class="delete-btn" @click="deleteBurger(pedido.id)">Cancelar</button>                           
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>
import MessageBurger from './MessageBurger.vue'
export default {
    name: 'DashboardView',
    data() {
        return {
            pedidos: null,
            pedidos_id: null,
            status: null,
            msg: '',
            mensagemBool: false,
            count: '',
        }
    },
    components: {
        MessageBurger
    },
    methods: {
        async getStatus(){
            const req = await fetch("http://localhost:3000/status")
            const data = await req.json()
            this.status = data
        }
        ,
        async getPedidos() {
            const req = await fetch("http://localhost:3000/burgers")
            const data = await req.json()
            this.pedidos = data
            this.count = data.length
        },
        async deleteBurger(id){
           const req = await fetch(`http://localhost:3000/burgers/${id}`,{
            method: 'DELETE'
           })
           const res = await req.json()
           this.getPedidos()

           this.mensagemBool = true
           this.msg = `O pedido Nº ${id} foi finalizado`
           setTimeout(() => {
                this.mensagemBool = false
           }, 4000);
        },
        async updateBurger(event, id){
            const option = event.target.value
            const dataJson = JSON.stringify({status: option})
            const req = await fetch(`http://localhost:3000/burgers/${id}`,{
                method: 'PATCH',
                headers: {"Content-Type" : "application/json"},
                body: dataJson
            })

            const res = req.json()
            if(option === 'Finalizado'){
                this.deleteBurger(id)
            }
        },

        novoPedido(){
            let id = this.ultimoPedido.id
            this.msg = `Novo pedido realizado - Pedido de Nº ${id}`
            this.mensagemBool = true
            setTimeout(() => {
                this.mensagemBool = false
            }, 4000);
        }

    },
    created() {

    },
    mounted() {
        setInterval(() => {
            this.getPedidos()
        }, 1000);
        this.getStatus()
    },
    watch:{
        pedidos(newValue,oldValue){
            if(newValue>oldValue){
                console.log(newValue)
                this.novoPedido()
            }
        } 
    },
    computed:{
        ultimoPedido(){
            if(this.pedidos){
                return this.pedidos[this.pedidos.length - 1]
            }
            return null
        }
    }
}
</script>

<style scoped>
table {
    width: 100%;
    border: 1px solid black;
    border-collapse: collapse;
}

thead {
    background: #ccc;
}

tr {
    border: 1px solid black;
}

td {
    border: 1px solid black;
    padding: 5px;
}

tr th {
    text-align: start;
    padding: 5px 0 5px 5px;
}

ul {
   padding-left: 20px;
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