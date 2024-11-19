<template>
  <div class="content">
    <div class="md-layout">
      <div class="md-layout-item md-size-100 mx-auto md-small-size-100">
        <md-card>
          <md-card-header data-background-color="purple">
            <h4 class="title">
              <strong>Upload or Download file at here</strong>
            </h4>
            <p class="category">Created by Diamond flashy</p>
          </md-card-header>

          <md-card-content>
            <div class="table-responsive table-upgrade">
              <table class="table" cellspacing="0">
                <thead>
                  <tr>
                    <th class="text-center"><strong>Name</strong></th>
                    <th class="text-center"><strong>Size</strong></th>
                    <th class="text-center"><strong>Action</strong></th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(file, index) in files" :key="index">
                    <td style="font-size: 15px">{{ file.name }}</td>
                    <td class="text-center">{{ file.size / 1024 }} KB</td>
                    <td class="text-center">
                      <md-button
                        @click="Downloadfile(file.name)"
                        class="md-warning md-round"
                        >Download</md-button
                      >
                    </td>
                  </tr>
                  <tr>
                    <td class="text-center">
                      <Alert
                        v-if="uploadStatus"
                        :message="uploadStatus.message"
                        :type="uploadStatus.type"
                      />
                    </td>
                    <td class="text-center">
                      <form @submit.prevent="uploadFile">
                        <input
                          type="file"
                          ref="fileInput"
                          @change="handleFileChange"
                          style="display: none"
                        />
                        <md-button
                          type="button"
                          class="md-info md-round"
                          @click="triggerFileInput"
                          >Upload File</md-button
                        >
                      </form>
                    </td>
                    <td class="text-center">
                      <md-button target="_blank" class="md-info md-round"
                        >Download All</md-button
                      >
                    </td>
                  </tr>
                </tbody>
              </table>
              <div v-if="isUploading" class="progress-bar-container">
                <div
                  class="progress-bar"
                  :style="{ width: uploadProgress + '%' }"
                ></div>
              </div>
            </div>
          </md-card-content>
        </md-card>
      </div>
    </div>
  </div>
</template>

<script>
import { Alert } from "@/components";

export default {
  components: {
    Alert,
  },
  data() {
    return {
      files: [],
      selectedFile: null,
      uploadStatus: null,
      isUploading: false,
      uploadProgress: 0,
    };
  },
  methods: {
    triggerFileInput() {
      this.$refs.fileInput.click();
    },
    handleFileChange(event) {
      this.selectedFile = event.target.files[0];
      this.uploadFile();
    },
    async uploadFile() {
      if (!this.selectedFile) return;

      const formData = new FormData();
      formData.append("file", this.selectedFile);
      const ip_dynamic = "http://10.59.101.12:8888/upload/";
      const ip = "http://10.59.101.12:8888/upload/";
      try {
        const response = await fetch(ip, {
          method: "POST",
          body: formData,
        });
        if (response.ok) {
          const result = await response.json();
          print(result);
          this.uploadStatus = {
            message: `Success: ${result.info}`,
            type: "success",
          };
          this.fetchFiles(); // Refresh the file list after uploading
        } else {
          throw new Error("Failed to upload file");
        }
      } catch (error) {
        print("Error uploading file:", error);
        this.uploadStatus = {
          message: "Error: Failed to upload file",
          type: "danger",
        };
      } finally {
        this.isUploading = false;
        this.uploadProgress = 0;
      }
    },
    async fetchFiles() {
      try {
        const response = await fetch("http://10.59.101.12:8888/files/");
        this.files = await response.json();
      } catch (error) {
        //console.log('Error fetching files:', error);
        //print('Error fetching files:', error);
      }
    },
    async Downloadfile(fileName) {
      try {
        const response = await fetch(
          `http://10.59.101.12:8888/download/${fileName}`,
          {
            method: "GET",
          }
        );
        if (!response.ok) {
          throw new Error("Network response was not ok");
        }
        const blob = await response.blob();
        const url = window.URL.createObjectURL(blob);
        const a = document.createElement("a");
        a.href = url;
        a.download = fileName;
        document.body.appendChild(a);
        a.click();
        window.URL.revokeObjectURL(url);
        document.body.removeChild(a);
      } catch (error) {
        //print('Error downloading file:', error);
      }
    },
  },
  created() {
    this.fetchFiles();
  },
};
</script>

<style lang="scss" scoped>
.mx-auto {
  margin-left: auto !important;
  margin-right: auto !important;
}

.md-card {
  margin-top: 50px;
}

.table-responsive {
  overflow: auto;
}

.table {
  width: 100%;
  max-width: 100%;
  margin-bottom: 1rem;
  background-color: transparent;

  thead tr th {
    font-size: 1.063rem;
    border-top-width: 0;
    font-weight: 300;
    border-bottom: 1px solid rgba(0, 0, 0, 0.06);
  }

  & > tbody > tr > td,
  & > tbody > tr > th,
  & > tfoot > tr > td,
  & > tfoot > tr > th,
  & > thead > tr > td,
  & > thead > tr > th {
    padding: 12px 8px;
    vertical-align: middle;
  }

  & > tbody > tr {
    position: relative;
  }

  td,
  th {
    padding: 0.75rem;
    vertical-align: top;
    border-top: 1px solid rgba(0, 0, 0, 0.06);
  }
  .progress-bar-container {
    position: relative;
    height: 30px;
    background-color: #e0e0e0;
    border-radius: 5px;
    overflow: hidden;
    margin-top: 10px;
  }

  .progress-bar {
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    background-color: purple;
    transition: width 0.4s ease;
  }
}
</style>
