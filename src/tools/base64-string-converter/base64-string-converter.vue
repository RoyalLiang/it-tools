<script setup lang="ts">
import { useCopy } from '@/composable/copy';
import { base64ToText, isValidBase64, textToBase64 } from '@/utils/base64';
import { withDefaultOnError } from '@/utils/defaults';

const encodeUrlSafe = useStorage('base64-string-converter--encode-url-safe', false);
const decodeUrlSafe = useStorage('base64-string-converter--decode-url-safe', false);

const textInput = ref('');
const base64Output = computed(() => textToBase64(textInput.value, { makeUrlSafe: encodeUrlSafe.value }));

const base64Input = ref('');
const textOutput = computed(() =>
  withDefaultOnError(() => base64ToText(base64Input.value.trim(), { makeUrlSafe: decodeUrlSafe.value }), ''),
);

const b64ValidationRules = [
  {
    message: 'Invalid base64 string',
    validator: (value: string) => isValidBase64(value.trim(), { makeUrlSafe: decodeUrlSafe.value }),
  },
];
const b64ValidationWatch = [decodeUrlSafe];
const { t } = useI18n();
const { copy: copyText } = useCopy({ source: textOutput, text: t('copy.copied') });
const { copy: copyTextBase64 } = useCopy({ source: base64Output, text: 'Base64 ' + t('copy.copied') });
</script>

<template>
  <c-card :title="t('tools.base64-string-converter.stringToBase64')">
    <n-form-item :label="t('tools.base64-string-converter.urlEncodeSafe')" label-placement="left">
      <n-switch v-model:value="encodeUrlSafe" />
    </n-form-item>
    <c-input-text
      v-model:value="textInput"
      multiline
      :placeholder="t('tools.base64-string-converter.inputString')"
      rows="5"
      :label="t('tools.base64-string-converter.stringEncode')"
      raw-text
      mb-5
    />

    <c-input-text
      :label="t('tools.base64-string-converter.base64String')"
      :value="base64Output"
      multiline
      readonly
      :placeholder="t('tools.base64-string-converter.base64ShowString')"
      rows="5"
      mb-5
    />

    <div flex justify-center>
      <c-button @click="copyTextBase64()">
        {{ t('tools.base64-string-converter.copyBase64') }}
      </c-button>
    </div>
  </c-card>

  <c-card :title="t('tools.base64-string-converter.base64ToString')">
    <n-form-item :label="t('tools.base64-string-converter.urlDecodeSafe')" label-placement="left">
      <n-switch v-model:value="decodeUrlSafe" />
    </n-form-item>
    <c-input-text
      v-model:value="base64Input"
      multiline
      :placeholder="t('tools.base64-string-converter.inputBase64')"
      rows="5"
      :validation-rules="b64ValidationRules"
      :validation-watch="b64ValidationWatch"
      :label="t('tools.base64-string-converter.base64Encode')"
      mb-5
    />

    <c-input-text
      v-model:value="textOutput"
      :label="t('tools.base64-string-converter.decodeString')"
      :placeholder="t('tools.base64-string-converter.decodeShow')"
      multiline
      rows="5"
      readonly
      mb-5
    />

    <div flex justify-center>
      <c-button @click="copyText()">
        {{ t('tools.base64-string-converter.copyString') }}
      </c-button>
    </div>
  </c-card>
</template>
