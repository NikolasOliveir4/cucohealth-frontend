<template>
    <b-modal id="modal-1" v-model="showModal" size="xl" footer-class="d-none">
        <div class="container">
            <div class="usuario">
                <div class="foto">
                    <img src="../assets/images/profile.jpg" alt="">
                </div>
                <div class="info">
                    <div>
                        <p>Nome</p>
                        <input type="text" class="nome" v-model="data.nome">
                    </div>
                    <div>
                        <p>Data de Nascimento</p>
                        <input type="date" class="data_nascimento">
                    </div>
                </div>
            </div>
            <div class="break"></div>
            <div class="docs">
                <div class="tipo">
                    <input type="radio" id="cpf" name="cpf_cnpj" value="cpf">
                    <label for="html">CPF</label><br>
                    <div class="break"></div>
                    <input type="radio" id="cnpj" name="cpf_cnpj" value="cnpj">
                    <label for="css">CNPJ</label><br>
                </div>
                <div class="documento">
                    <p>CPF / CNPJ</p>
                    <input type="number" name="documento" id="documento">
                </div>
            </div>
            <div class="break"></div>
            <div class="contato">
                <div class="email">
                    <p>E-mail</p>
                    <input type="email" name="email" id="email">
                </div>
                <div class="telefone">
                    <p>Telefone</p>
                    <input type="number" name="telefone" id="telefone">
                </div>
            </div>
            <div class="break"></div>
            <div class="enviar">
                <input type="button" value="ENVIAR">
            </div>
        </div>
    </b-modal>
</template>

<script>
export default {
    name: 'CardClient',
    props:{
        showModal:{
            type: Boolean,
            default: false,
        },
        data:{
            type: Object,
        },
    },
    data(){
        return{
            openModal: this.showModal || false,
        };
    },
};
</script>

<style>

</style>