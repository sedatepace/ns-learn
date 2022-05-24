<template>
    <Page class="page">
      <ActionBar class="action-bar">
        <NavigationButton visibility="hidden"/>
        <GridLayout columns="50, *">
          <Label class="action-bar-title" text="FileSystem" colSpan="2"/>

          <Label class="fas" text.decode="&#xf0c9;" @tap="onDrawerButtonTap"/>
        </GridLayout>
      </ActionBar>

        <StackLayout class="page__content">
            <Label class="common" :text="list[0].path" textWrap="true" />
            <Label class="common" :text="JSON.stringify(list)" textWrap="true"/>
            <Button
                text="저장하기."
                @tap="saveBtn"
                borderWidth="2"
                borderColor="#000"
                width="100%"
            />

            <Button
                text="불러오기."
                @tap="loadBtn"
                borderWidth="2"
                borderColor="#000"
                width="100%"
            />

        </StackLayout>
    </Page>
</template>

<script>
  import * as utils from "~/shared/utils";
  import { SelectedPageService } from "../shared/selected-page-service";
  
  import * as fileSystemModule from '@nativescript/core/file-system';
    

  export default {
    mounted() {
        SelectedPageService.getInstance().updateSelectedPage("Home");
        this.loadBtn();
    },
    computed: {
      message() {
        return "<!-- Page content goes here -->";
      }
    },
    methods: {
        onDrawerButtonTap() {
            utils.showDrawer();
        },

        saveBtn(){
            var vm = this;
            var fileName = "data.json";
            const documents = fileSystemModule.knownFolders.documents();
            const file = documents.getFile(fileName);
            var sam = {
                time:123456,
                memo:"0000"
            }
            this.list[0].timeList.push(sam);
            console.log(sam);
            file.writeText(JSON.stringify(this.list)).then((res)=>{
                console.log("WriteText OK");
            })
            .catch((err)=>{
                console.log("WriteText_ERR");
                console.log({err})
            })
        }, //saveBtn

        loadBtn(){
            try{
                var vm = this;
                var fileName = "data.json";
                const documents = fileSystemModule.knownFolders.documents();
                const file = documents.getFile(fileName);
                file.readText().then(function(content){
                    // vm.msg = content;
                    console.log("readText ok");
                    console.log({content});
                    vm.list = JSON.parse(content)
                })
            }catch(err){
                console.log({err});
            }
            

        }



    },
    data(){
        return  {
            dataSample : {
                    time: 1234511,
                    memo: "etc111"
            },

            list : [
                    { 
                        path:"/123.mp3",
                        timeList: [{
                            time:12345,
                            memo:"etc"
                        }]        
                    }
                ],

        }
    }
  };
</script>

<style scoped lang="scss">
    // Start custom common variables
    @import '@nativescript/theme/scss/variables/blue';
    // End custom common variables
    .common{
        text-align: center;
        word-wrap:break-word;
    }
    // Custom styles
</style>
