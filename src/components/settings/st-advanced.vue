<template>
  <div>
    <div class="bd-1">
      <div class="pd-20">
        <h3>Directory Listing</h3>
        <div class="gray mt-1 fz-14">
          If no index file is present within a directory, the directory contents
          will be displayed.
        </div>
        <div class="mt-3 hide-msg">
          <v-switch
            v-model="directoryList"
            :label="directoryList ? 'Enabled' : 'Disabled'"
          ></v-switch>
        </div>
      </div>
      <!-- <div class="pd-15-20 bdt-1 bg-f8">
        <div class="gray fz-12">
          Learn more about <a href="" target="_blank">Directory Listing</a>
        </div>
      </div> -->
    </div>

    <div class="bd-1 pd-20 mt-5 d-flex al-c">
      <div class="mr-8">
        <h3>Your Statistics</h3>
        <div class="gray mt-1 fz-14">
          DApps statistics are displayed on the 4EVERLAND website. If you don't
          want to display it, close the button to hide DApps statistics, but all
          your data is still visible.
        </div>
      </div>
      <v-switch class="ml-auto" v-model="isStatis" disabled></v-switch>
    </div>

    <div class="bd-1 mt-5">
      <div class="pd-20">
        <h3>Delete Project</h3>
        <div class="gray mt-1 fz-14">
          The project will be deleted permanently including deployments and
          domains. You can't undo this action.
        </div>
      </div>
      <div class="pd-10-20 bdt-1 bg-f8 d-flex">
        <v-btn color="error" small class="ml-auto" @click="onDelete"
          >Delete</v-btn
        >
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    info: Object,
  },
  data() {
    const { directoryList } = this.info.config;
    return {
      directoryList,
      isStatis: true,
    };
  },
  computed: {
    userInfo() {
      return this.$store.state.userInfo;
    },
  },
  watch: {
    async directoryList(val) {
      try {
        this.$loading();
        await this.saveProject({
          directoryList: val,
        });
        this.$loading.close();
      } catch (error) {
        this.directoryList = !val;
      }
    },
  },
  methods: {
    async onDelete() {
      try {
        let html =
          "4everland will delete all of your projects，along with all of its Deployments, Domains, SSL Certificates, and all other resources belonging to your project.";
        html +=
          '<div class="bg-warning pd-10-20 fz-14 mt-3"><b>Warning</b>: This action is not reversible.Please be certain</div>';
        const { name } = this.info;
        await this.$prompt(html, "Delete Project", {
          confirmText: "Delete",
          confirmTextAttrs: {
            color: "error",
            text: false,
          },
          inputAttrs: {
            label: `Enter project name \`${name}\` to continue`,
            rules: [(v) => v == name || "The text you entered didn't match."],
            required: true,
          },
        });
        this.$loading();
        await this.$http.delete("/project/" + this.info.id);
        this.$loading.close();
        await this.$alert("Project deleted successfully", "Done", {
          type: "success",
        });
        // this.$router.replace('/')
        location = "index.html";
      } catch (error) {
        console.log(error);
      }
    },
    async saveProject(body) {
      return this.$http.put("/project/config/" + this.info.id, body);
    },
  },
};
</script>