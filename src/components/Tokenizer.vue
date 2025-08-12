<script setup>
import { computed, ref } from "vue";

defineProps({
    msg: String,
});

const vocab =
    "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890!@#$%^&*()_+-= ".split(
        ""
    );

const char2id = {};
const id2char = {};

vocab.forEach((char, idx) => {
    char2id[char] = idx;
    id2char[idx] = char;
});

const inputText = ref("");
const outputText = computed(() => {
    const tokens = [];
    for (const char of inputText.value) {
        if (char2id.hasOwnProperty(char)) {
            tokens.push(char2id[char]);
        } else {
            tokens.push("<UNK>");
        }
    }
    return tokens;
});

const inputToken = ref("");
const outputToken = computed(() => {
    let decoded = "";
    const inputTokens = inputToken.value
        .split(",")
        .map((item) => item.trim())
        .filter((item) => item !== "")
        .map((item) => (item === "<UNK>" ? "<UNK>" : parseInt(item, 10)))
        .filter((val) => val === "<UNK>" || !isNaN(val));

    for (const tok of inputTokens) {
        if (tok >= 0 && tok < vocab.length) {
            decoded += id2char[tok];
        } else {
            decoded += "?";
        }
    }

    return decoded;
});
</script>

<template>
    <h1 class="mb-5">{{ msg }}</h1>

    <div class="container">
        <div class="row">
            <div class="col-md-6 mb-4">
                <div class="card">
                    <div class="card-header">Encoder</div>
                    <div class="card-body">
                        <div class="mb-3">
                            <label for="inputText" class="form-label"
                                >Enter Text</label
                            >
                            <textarea
                                v-model="inputText"
                                type="text"
                                class="form-control"
                                id="inputText"
                                placeholder="Ex. Hello"
                            />
                        </div>
                        <div class="mb-3">
                            <label for="outputText" class="form-label"
                                >Encoded Tokens</label
                            >
                            <textarea
                                v-model="outputText"
                                type="text"
                                class="form-control"
                                id="outputText"
                                disabled
                            />
                        </div>
                    </div>
                </div>
            </div>
            <div class="col-md-6 mb-4">
                <div class="card">
                    <div class="card-header">Decoder</div>
                    <div class="card-body">
                        <div class="mb-3">
                            <label for="inputToken" class="form-label"
                                >Enter Tokens</label
                            >
                            <textarea
                                v-model="inputToken"
                                type="text"
                                class="form-control"
                                id="inputToken"
                                placeholder="Ex. 33,4,11,11,14"
                            />
                        </div>
                        <div class="mb-3">
                            <label for="outputToken" class="form-label"
                                >Decoded Text</label
                            >
                            <textarea
                                v-model="outputToken"
                                type="text"
                                class="form-control"
                                id="outputToken"
                                disabled
                            />
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped></style>
