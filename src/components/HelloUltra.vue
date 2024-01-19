<template>
  <v-container class="fill-height">
    <v-responsive class="align-center text-center fill-height">
      <v-row class="d-flex align-center justify-center">
        <v-col cols="auto">
          <v-btn color="primary" variant="flat" @click="handleMagnetLink(false)">
            Wake up Ultra background
          </v-btn>
        </v-col>
        <v-col cols="auto">
          <v-btn color="primary" variant="flat" @click="handleMagnetLink(true)">
            Wake up Ultra foreground
          </v-btn>
        </v-col>
        <v-col cols="auto">
          <v-btn color="primary" variant="flat" @click="getSystems">
            Get supported systems
          </v-btn>
        </v-col>
        <v-col cols="auto">
          <v-btn color="primary" variant="flat" @click="getAllVerificationRules">
            Get all verification rules
          </v-btn>
        </v-col>
        <v-col cols="auto">
          <v-btn color="primary" variant="flat" @click="getVerificationRules">
            Get verification rules
          </v-btn>
        </v-col>
        <v-col cols="auto">
          <v-btn color="primary" variant="flat" @click="getDesignTemplate">
            Use template to create me a design
          </v-btn>
        </v-col>
        <v-col cols="auto">
          <v-btn color="primary" variant="flat" @click="postVerifyDesign">
            Verify Design
          </v-btn>
        </v-col>
        <v-col cols="auto">
          <v-btn color="primary" variant="flat" @click="startUploadDesign">
            Start upload design via Ultra task
          </v-btn>
        </v-col>
        <v-col cols="auto">
          <v-btn color="primary" variant="flat" @click="getTasksStatus">
            Get tasks status
          </v-btn>
        </v-col>
      </v-row>

      <v-row class="d-flex align-center justify-center">
        <v-divider></v-divider>
      </v-row>
      <v-row class="d-flex align-center justify-center">
        <v-col cols="auto">
          <v-card class="mx-auto" variant="outlined" max-width="800">
            <v-card-item>
              <div>
                <div class="text-overline mb-1">
                  {{ endpoint }} {{ status }}
                </div>
                <div class="text-caption">
                  {{ responseData }}
                </div>
              </div>
            </v-card-item>
          </v-card>
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
  <iframe style="display: none" name="magnetframe"></iframe>
</template>

<script setup lang="ts">
import axios, { AxiosError } from "axios";
import { ref } from "vue";

const server = ref("http://localhost:8082/");
const status = ref("");
const endpoint = ref("");
const responseData = ref("");
const responseId = ref(0);

const get = async (url: string) => {
  try {
    const response = await axios.get(`${server.value}${url}`);
    console.log(response);
    status.value = response.status.toString();
    endpoint.value = response.request.responseURL.toString();
    responseData.value = JSON.stringify(response.data, null, 4);
  } catch (error) {
    handleError(error)
  }
};

const post = async (url: string, body: unknown) => {
  try {
    const response = await axios.post(`${server.value}${url}`, body, {
      headers: {
        "Content-Type": "application/json",
        Accept: "application/json",
      },
    });
    status.value = response.status.toString();
    endpoint.value = response.request.responseURL.toString();
    if (response.data.id) {
      responseId.value = response.data.id;
    }
    responseData.value = response.data;
  } catch (error) {
    handleError(error)
  }
};
const handleError = (error: unknown) => {
  console.error("Error:", error);
  const newError = error as AxiosError
  if (newError) {
    if (newError.response)
      status.value = newError.response.status.toString();
    endpoint.value = newError.request.responseURL.toString();
    if (newError.response?.data as string)
      responseData.value = newError.response?.data as string
  }

}

const handleMagnetLink = (bringToFront: boolean) => {
  const magnetLink = `vsultra://?bringToFront=${bringToFront}`;
  window.open(magnetLink, "magnetframe");
};

const getSystems = () => {
  get("designs/systems");
};

const getAllVerificationRules = () => {
  get("designs/verification-rules");
};

const getVerificationRules = () => {
  get("designs/verification-rules/commander4g");
};

const getDesignTemplate = async () => {
  const body = {
    name: "QuarryDesign",
    designPattern: "square",
    rowsPerDesign: 1,
    holesPerRow: 5,
    detsPerDeck: 2,
    decksPerHole: 3,
    burden: 0.8,
    spacing: 0.8,
    holeDiameter: 5.637,
    holeDepth: 5.5,
    holeBearing: 2.58,
    holeAngle: 90.5,
  };
  await post("designs/template", body);
};

const postVerifyDesign = async () => {
  if (responseId.value === null) {
    await getDesignTemplate();
  }
  get(`designs/${responseId.value}/verify`);
};

const startUploadDesign = () => {
  post("devices/tasks/upload-with-ui", {
    designid: 1,
  });
};

const getTasksStatus = () => {
  get("devices/tasks");
};
</script>
