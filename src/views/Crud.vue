<template>
    <div>
        <h1>CRUD</h1>
        <ul>
            <li v-for="(item, index) in entradas" :key="index">

                {{item.id}} - {{item.title}} - {{item.date}} -- {{item.status}}

            </li>
        </ul>
    </div>
</template>
<script>
export default {
    data(){
        return{
            entradas:[]
        }
    },
    created(){
        this.getEntradas();
    },
    methods:{
        async getEntradas(){
            try {
                const entradasDB = await this.axios.get('wp/v2/posts');

                //console.log(entradasDB.data);

                await entradasDB.data.forEach(element => {
                    //console.log(element)
                    let  item={}
                    item.id = element.id
                    item.title = element.title.rendered;
                    item.content = element.content.rendered;
                    item.date = element.date;
                    item.status = element.status
                    this.entradas.push(item);
                });

            } catch (error) {
                console.log(error)
            }
        }
    }
}
</script>