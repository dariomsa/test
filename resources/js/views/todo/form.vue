<template>
    <form @submit.prevent="proceed" @keydown="todoForm.errors.clear($event.target.name)">

        <div class="row">
            <div class="col-12 col-sm-4">
                <div class="form-group">
                    <label for="">Medio</label>
                    <select v-model="todoForm.medio" class="custom-select col-12">
                                            <option v-for="option in medios" v-bind:value="option.value">
                                                {{ option.text }}
                                            </option>
                    </select>
                    <!-- <show-error :form-name="todoForm" prop-name="medio">ddd</show-error> -->
                </div>
            </div>
            <div class="col-12 col-sm-4">
                <div class="form-group">
                    <label for="">Dispositivo</label>
                    <select v-model="todoForm.dispositivo" class="custom-select col-12">
                                            <option v-for="option2 in dispositivos" v-bind:value="option2.value">
                                                {{ option2.text }}
                                            </option>
                    </select>
                </div>
            </div>
            <div class="col-12 col-sm-4">
                 <div class="form-group">
                    <label for="">Medio</label>
                    <select v-model="todoForm.medio" class="custom-select col-12">
                                            <option v-for="option in medios" v-bind:value="option.value">
                                                {{ option.text }}
                                            </option>
                    </select>
                    <!-- <show-error :form-name="todoForm" prop-name="medio">ddd</show-error> -->
                </div>
            </div>
        </div>

        <div class="row">
            <div class="col-12 col-sm-4">
                <div class="form-group">
                    <label for="">Formato</label>
                    <input class="form-control" type="text" value="" v-model="todoForm.title" name="title">
                    <show-error :form-name="todoForm" prop-name="title"></show-error>
                </div>
            </div>
            <div class="col-12 col-sm-4">
                <div class="form-group">
                    <label for="">Fecha Inicio</label>
                    <datepicker v-model="todoForm.date" :bootstrapStyling="true" @selected="todoForm.errors.clear('date')" ></datepicker>
                    <show-error :form-name="todoForm" prop-name="date"></show-error>
                </div>
            </div>
             <div class="col-12 col-sm-4">
                <div class="form-group">
                    <label for="">Fecha Fin</label>
                    <datepicker v-model="todoForm.date" :bootstrapStyling="true" @selected="todoForm.errors.clear('date')" ></datepicker>
                    <show-error :form-name="todoForm" prop-name="date"></show-error>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-12 col-sm-4">
                <div class="form-group">
                    <label for="">Impresiones</label>
                    <input class="form-control" type="text" value="" v-model="todoForm.title" name="title" >
                    <show-error :form-name="todoForm" prop-name="title"></show-error>
                </div>
            </div>
            <div class="col-12 col-sm-8">
                <div class="form-group">
                    <label for="">URL</label>
                    <input class="form-control" type="text" value="" v-model="todoForm.title" name="title" >
                    <show-error :form-name="todoForm" prop-name="date"></show-error>
                </div>
            </div>
            
        </div>

         <div class="row">
            <div class="col-12 col-sm-4">
                <div class="form-group">
                    <label for="">Frecuencia</label>
                    <autosize-textarea v-model="todoForm.description" rows="1" name="description" ></autosize-textarea>
                    <show-error :form-name="todoForm" prop-name="description"></show-error>
                </div>
            </div>
            <div class="col-12 col-sm-4">
                <div class="form-group">
                    <label for="">Segmentación</label>
                    <autosize-textarea v-model="todoForm.description" rows="1" name="description" ></autosize-textarea>
                    <show-error :form-name="todoForm" prop-name="description"></show-error>
                </div>
            </div>
            <div class="col-12 col-sm-4">
                <div class="form-group">
                    <label for="">Observaciones</label>
                    <autosize-textarea v-model="todoForm.description" rows="1" name="description" ></autosize-textarea>
                    <show-error :form-name="todoForm" prop-name="description"></show-error>
                </div>
            </div>
        </div>
        <button type="submit" class="btn btn-info waves-effect waves-light pull-right">
            <span v-if="id">{{trans('general.update')}}</span>
            <span v-else>{{trans('general.save')}}</span>
        </button>
        <router-link to="/todo" class="btn btn-danger waves-effect waves-light pull-right m-r-10" v-show="id">{{trans('general.cancel')}}</router-link>
        <button v-if="!id" type="button" class="btn btn-danger waves-effect waves-light pull-right m-r-10" @click="$emit('cancel')">{{trans('general.cancel')}}</button>
    </form>
</template>


<script>
    import datepicker from 'vuejs-datepicker'
    import autosizeTextarea from '../../components/autosize-textarea'

    export default {
        components: {datepicker,autosizeTextarea},
        data() {
            return {
                todoForm: new Form({
                    title : '',
                    description : '',
                    date: '',
                    driver : ''
                }),
                 medios: [],
                 dispositivos: []
            };
        },
        props: ['id'],
        mounted() {
            if(this.id){
            this.getTodo();
            }
            axios.get('/api/configuration/variable?type=medios')
                .then(response => response.data)
                .then(response => {
                    this.medios = response.medios;
                })
                .catch(error => {
                    helper.showDataErrorMsg(error);
                });
            axios.get('/api/configuration/variable?type=dispositivos')
                .then(response => response.data)
                .then(response => {
                    this.dispositivos = response.dispositivos;
                })
                .catch(error => {
                    helper.showDataErrorMsg(error);
                });    
        },
        methods: {
            proceed(){
                if(this.id)
                    this.updateTodo();
                else
                    this.storeTodo();
            },
            storeTodo(){
                this.todoForm.date = moment(this.todoForm.date).format('YYYY-MM-DD');
                this.todoForm.post('/api/todo')
                    .then(response => {
                        toastr.success(response.message);
                        this.$emit('completed')
                    })
                    .catch(error => {
                        helper.showErrorMsg(error);
                    });
            },
            getTodo(){
                axios.get('/api/todo/'+this.id)
                    .then(response => response.data)
                    .then(response => {
                        this.todoForm.title = response.title;
                        this.todoForm.description = response.description;
                        this.todoForm.date = response.date;
                    })
                    .catch(error => {
                        helper.showDataErrorMsg(error);
                        this.$router.push('/todo');
                    });
            },
            updateTodo(){
                this.todoForm.date = moment(this.todoForm.date).format('YYYY-MM-DD');
                this.todoForm.patch('/api/todo/'+this.id)
                    .then(response => {
                        toastr.success(response.message);
                        this.$router.push('/todo');
                    })
                    .catch(error => {
                        helper.showErrorMsg(error);
                    });
            }
        }
    }
</script>
