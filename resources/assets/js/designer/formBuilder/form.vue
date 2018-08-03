<template>
    <div id="form-container">
        <nav class="navbar  navbar-expand-lg  navbar navbar-dark bg-dark">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                    <a class="nav-link" @click="mode = 'editor'" href="#">Editor</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" @click="mode = 'preview'" href="#">Preview</a>
                </li>
            </ul>
            <ul class="navbar-nav  pull-right">
                <li class="nav-item">
                    <a class="nav-link" @click="saveForm" href="#">Save</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" @click="onClose" href="#">Close</a>
                </li>
            </ul>
        </nav>
        <vue-form-builder @change="updateConfig" ref="formbuilder" v-show="mode == 'editor'" config="config"/>
        <div id="preview" v-if="mode == 'preview'">
            <div id="data-input">
                <div class="card-header">
                Data Input
                </div>
                <div class="alert" :class="{'alert-success': previewInputValid, 'alert-danger': !previewInputValid}">
                <span v-if="previewInputValid">Valid JSON Data Object</span>
                <span v-else>Invalid JSON Data Object</span>
                </div>
                <form-text-area rows="20" v-model="previewInput"></form-text-area>

            </div>

            <div id="renderer-container">
                <div class="container">
                    <div class="row">
                        <div class="col-sm">
                            <vue-form-renderer @submit="previewSubmit" v-model="previewData" v-if="mode == 'preview'" :config="config"/>
                        </div>
                    </div>
                </div>
            </div>
            <div id="data-preview">
                <div class="card-header">
                    Data Preview
                </div>
                <vue-json-pretty :data="previewData"></vue-json-pretty>
            </div>
        </div>
    </div>
</template>

<script>
    import VueFormBuilder from "@processmaker/vue-form-builder/src/components/vue-form-builder";
    import VueFormRenderer from "@processmaker/vue-form-builder/src/components/vue-form-renderer";
    import VueJsonPretty from 'vue-json-pretty';
    import {FormTextArea} from '@processmaker/vue-form-elements/src/components';

    export default {
        data() {
            return {
                mode: "editor",
                config: [
                    {
                        name: "Default",
                        items: []
                    }
                ],
                previewData: null,
                previewInput: null
            };
        },
        components: {
            VueFormRenderer,
            VueFormBuilder,
            VueJsonPretty,
            FormTextArea
        },
        watch: {
            previewInput() {
                if(this.previewInputValid) {
                    // Copy data over
                    this.previewData = JSON.parse(this.previewInput)
                } else {
                    this.previewData = null
                }
            }
        },
        computed: {
            previewInputValid() {
                try {
                    JSON.parse(this.previewInput)
                    return true
                } catch(err) {
                    return false
                }
            }
        },
        props: [
            'process',
            'form'
        ],
        mounted() {
            this.$refs.formbuilder.config = this.form.content ? this.form.content : [
                {
                    name: "Default",
                    items: []
                }
            ];
        },
        methods: {
            updateConfig(newConfig) {
                this.config = newConfig
            },
            updatePreview(data) {
                this.previewData = data
            },
            previewSubmit() {
                alert("Preview Form was Submitted")
            },
            onClose() {
                window.location.href = '/designer/' + this.process.uid;
            },
            saveForm() {
                ProcessMaker.apiClient
                    .put(
                        'process/' +
                        this.process.uid +
                        '/form/' +
                        this.form.uid
                        ,
                        {
                            content: this.config
                        }
                    )
                    .then(response => {
                        ProcessMaker.alert(' Successfully saved', 'success');
                    });
            }
        }
    };
</script>

<style lang="scss">

    html,
    body {
        height: 100%;
        min-height: 100%;
        max-height: 100%;
        overflow: hidden;
    }

    #form-container {
        height: 100%;
        display: flex;
        flex-direction: column;
    }

    nav {
        min-height: 56px;
    }

    #preview {
        display: flex;
        flex-grow: 1;

        #renderer-container {
            flex-grow: 1;
            padding-top: 32px;
        }

        #data-input {
            min-width: 340px;
            width: 340px;
            max-width: 340px;
            border-right: 1px solid #e9edf1;
            overflow: auto;
        }

        #data-preview {
            height: 100%;
            min-width: 340px;
            width: 340px;
            max-width: 340px;
            border-left: 1px solid #e9edf1;
            overflow: auto;
        }
    }

    .inspector-container {
        min-width: 340px;
        width: 340px;
        max-width: 340px;
        border-left: 1px solid #e9edf1;
        overflow: auto;
    }
</style>