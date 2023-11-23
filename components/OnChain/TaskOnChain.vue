<template>
  <div class="conditions-container" :class="{ conditionsContainerEdit: !!props.edit }" @click="handleClick">
    <ConditionSelect :fullWidth="!props.edit ? true : false" :bg="!!props.edit ? true : false"
      :selected="selectedCondition" @select="setSelectCondition" :options="conditions" />
  </div>
  <div class="condition-setting" :class="{ conditionSettingEdit: !!props.edit }">
    <span v-if="!props.edit" class="condition-setting-title">{{ selectedCondition.subtitle }}</span>

    <div class="condition-setting-item blockchain-item">
      <div class="condition-setting-item-title">Blockchain</div>

      <div class="blockchain-select" :class="{ edit: !!props.edit }" @click="handleClick">
        <div class="blockchain-select-icon">
          <Blockchain :name="selectedBlockchain.icon" width="24" height="24" :alt="selectedBlockchain.name" />
        </div>
        <ConditionSelect :disabled="selectedCondition.value === ON_CHAIN_CONDITIONS.USER_HAS_ENS_DOMAIN"
          @select="setSelectBlockchain" :selected="selectedBlockchain" :options="blockchains" :fullWidth="false"
          :bg="false" />
      </div>
    </div>
    <div v-if="selectedCondition.value === ON_CHAIN_CONDITIONS.USER_HOLDS_TOKEN">
      <div class="flex items-center gap-2 condition-setting-item-title">
        Native Token
        <Switch :checked-prop="isNativeToken" @checked="value => emit('updateIsNativeToken', value)" />
      </div>
    </div>
    <div v-if="selectedCondition.value === ON_CHAIN_CONDITIONS.USER_HOLDS_TOKEN ? !isNativeToken : fieldInTheFormScope(ON_CHAIN_CONDITION_PARTS.CONTRACT_ADDRESS, props.selectedCondition)"
      class="condition-setting-item">
      <div class="condition-setting-item-title">
        {{ getLabelContractAddress(selectedCondition) }}
      </div>
      <Input @click="handleClick" :modelValue="contractAddress" :isError="validationErrors.contractAddressOnChain.isError &&
        validationErrors.contractAddressOnChain.isTriggered
        " @update:modelValue="(target) => emit('updateContractAddress', target)"
        :errorText="validationErrors.contractAddressOnChain.text"
        :placeholder="`Enter ${getLabelContractAddress(selectedCondition)}`" name="" />
    </div>
    <div v-if="fieldInTheFormScope(ON_CHAIN_CONDITION_PARTS.CURRENCY_ADDRESS, props.selectedCondition)"
      class="condition-setting-item">
      <CheckboxLabel text="Native token balance" :isTooltipActive="false" />
    </div>

    <!-- <div
      v-if="fieldInTheFormScope(ON_CHAIN_CONDITION_PARTS.EVENT_URL, props.selectedCondition)"
      class="condition-setting-item"
    >
      <div class="condition-setting-item-title">Event URL</div>
      <Input @click="handleClick" placeholder="Enter Event URL" name="" />
    </div> -->

    <div v-if="fieldInTheFormScope(ON_CHAIN_CONDITION_PARTS.TOKEN_ID, props.selectedCondition)"
      class="condition-setting-item amount">
      <div class="condition-setting-item-title">Token ID</div>

      <NumberInput @click="handleClick" :modelValue="tokenId" :noArrows="true" updateTokenId :step="0.01"
        :allowDecimal="true" @update:modelValue="(value) => emit('updateTokenId', value)" placeholder="Enter Token ID"
        type="number" name="" />
      <div v-if="validationErrors.tokenId.isError && validationErrors.tokenId.text" class="select-error">
        {{ validationErrors.tokenId.text }}
      </div>
    </div>
    <div v-if="fieldInTheFormScope(ON_CHAIN_CONDITION_PARTS.NFT_PREVIEW, props.selectedCondition)">
      <NFTPreview :bordered="true" :blockchain="props.selectedBlockchain.name" :contract-address="props.contractAddress"
        :token-id="props.tokenId" @update:nfName="value => emit('updateNftName', value)"
        @update:imgUrl="value => emit('updateNftPreview', value)" />
    </div>
    <div v-if="fieldInTheFormScope(ON_CHAIN_CONDITION_PARTS.SENDER_ADDRESS, props.selectedCondition)"
      class="condition-setting-item">
      <div class="condition-setting-item-title">Sender address</div>
      <Input @click="handleClick" :modelValue="senderAddress" :isError="validationErrors.senderAddress.isError && validationErrors.senderAddress.isTriggered
        " :errorText="validationErrors.senderAddress.text"
        @update:modelValue="(value) => emit('updateSenderAddress', value)" placeholder="Enter Sender Address" name="" />
    </div>
    <div v-if="fieldInTheFormScope(ON_CHAIN_CONDITION_PARTS.RECEIVER_ADDRESS, props.selectedCondition)"
      class="condition-setting-item">
      <div class="condition-setting-item-title">Receiver address</div>
      <Input @update:modelValue="(value) => emit('updateReceiverAddress', value)" placeholder="Enter Receiver address"
        :modelValue="receiverAddress" @click="handleClick" :isError="validationErrors.receiverAddress.isError && validationErrors.receiverAddress.isTriggered
          " :errorText="validationErrors.receiverAddress.text" name="" />
    </div>

    <div v-if="fieldInTheFormScope(ON_CHAIN_CONDITION_PARTS.CURRENCY_ADDRESS, props.selectedCondition)"
      class="condition-setting-item">
      <div class="condition-setting-item-title">
        Transferred ERC20/NFT/POAP Address (leave empty for Native token)
      </div>
      <Input @click="handleClick" :modelValue="currencyAddress" :isError="validationErrors.currencyAddress.isError && validationErrors.currencyAddress.isTriggered
        " :errorText="validationErrors.currencyAddress.text" placeholder="Enter ERC20/NFT/POAP Address"
        @update:modelValue="(value) => emit('updateCurrencyAddress', value)" name="" />
    </div>
    <div v-if="fieldInTheFormScope(ON_CHAIN_CONDITION_PARTS.AMOUNT, props.selectedCondition)"
      class="condition-setting-item">
      <div class="condition-setting-item-title">{{ getLabelAmount(selectedCondition) }}</div>
      <NumberInput @click="handleClick" :modelValue="amountCondition"
        @update:modelValue="(target) => emit('updateAmountCondition', target)" :allowDecimal="true" placeholder=""
        name="amount" type="number" :step="1" />
      <div v-if="validationErrors.amountCondition.isError && validationErrors.amountCondition.text" class="select-error">
        {{ validationErrors.amountCondition.text }}
      </div>
    </div>
    <div v-if="fieldInTheFormScope(ON_CHAIN_CONDITION_PARTS.TOKEN_NAME, props.selectedCondition)"
      class="condition-setting-item mb-4">
      <TokenPreview :token-address="contractAddress" :blockchain="selectedBlockchain.name"
        :quantity="amountCondition || 0" @update:tokenName="value => emit('updateTokenName', value)" />
    </div>
    <div class="divider" v-if="isDividerVisible"></div>
    <CompletionLink :showTitleInput="true" :link="buttonLink" :title="buttonTitle"
      :errors="{ actionButtonLink: validationErrors.actionButtonLink, actionButtonTitle: validationErrors.actionButtonTitle }"
      @change:title="value => emit('updateButtonTitle', value)" @change:link="value => emit('updateButtonLink', value)" />
  </div>
</template>

<script setup>
const setSelectCondition = (value) => {
  emit('updateSelectedCondition', value);

  if (value.value === ON_CHAIN_CONDITIONS.USER_HAS_ENS_DOMAIN) {
    emit('updateSelectedBlockchain', ethereumBlockchain);
  } else {
    emit('updateSelectedBlockchain', blockchains[0]);
  }
};

const setSelectBlockchain = (value) => {
  emit('updateSelectedBlockchain', value);
};

const getLabelContractAddress = (data) => {
  const { value } = data;

  switch (value) {
    case ON_CHAIN_CONDITIONS.ANY_CHAIN_ACTION:
      return 'Interacted Contract Address';
      break;
    case ON_CHAIN_CONDITIONS.USER_HOLDS_POAP:
      return 'POAP Token Contract Address';
      break;
    default:
      return 'Token Contract Address';
  }
};

const getLabelAmount = (data) => {
  const { value } = data;

  switch (value) {
    case ON_CHAIN_CONDITIONS.USER_HOLDS_TOKEN:
      return 'Minimum amount of the token balance';
      break;
    case ON_CHAIN_CONDITIONS.ANY_CHAIN_ACTION:
      return 'Minimum amount of interaction';
      break;
    case ON_CHAIN_CONDITIONS.USER_HOLDS_POAP:
      return 'POAP Token Contract Address';
      break;
    default:
      return 'Amount of Transfer';
  }
};

import { ON_CHAIN_CONDITION_PARTS, ON_CHAIN_CONDITIONS, fieldInTheFormScope } from './utils';
import { blockchains, ethereumBlockchain } from '~/util/onchain';

const handleClick = () => {
  if (props.handleIsOnChainEdit) {
    props.handleIsOnChainEdit();
  }
};

const props = defineProps([
  'handleIsOnChainEdit',
  'selectedBlockchain',
  'selectedCondition',
  'validationErrors',
  'amountCondition',
  'currencyAddress',
  'contractAddress',
  'receiverAddress',
  'senderAddress',
  'conditions',
  'tokenId',
  'edit',
  'buttonLink',
  'buttonTitle',
  'isNativeToken',
  'isDividerVisible'
]);

const emit = defineEmits([
  'updateAmountCondition',
  'updateTokenId',
  'updateContractAddress',
  'updateSenderAddress',
  'updateReceiverAddress',
  'updateSelectedCondition',
  'updateCurrencyAddress',
  'updateSelectedBlockchain',
  'updateButtonLink',
  'updateButtonTitle',
  'updateTokenName',
  'updateIsNativeToken',
  'updateNftName',
  'updateNftPreview'
]);

</script>

<style lang="scss" scoped>
.on-chain-container-titles {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.conditions-container {
  &>.custom-select-condition {
    padding-left: 8px;
  }
}

.edit-condition-modal {
  .conditions-container {
    width: 100%;
    margin-bottom: 12px;
  }

  .conditionsContainerEdit {
    margin-bottom: 12px;
  }

 
}

.divider {
  height: 1px;
  background: #DAD9F7;
}

.condition-setting {
  background-color: #eaeafb;
  padding: 16px;
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  gap: 16px;

  &-title {
    color: #6a6d8f;
    font-size: 16px;
    font-style: normal;
    font-weight: 400;
    line-height: 24px;
  }

  &-item {
    &-title {
      margin-bottom: 8px;
      font-size: 14px;
      font-style: normal;
      font-weight: 500;
      line-height: 142.857%;
      color: #1a1d29;
    }

    .blockchain-select {
      position: relative;
      width: 50%;

      &-icon {
        position: absolute;
        height: 100%;
        left: 0;
        width: 40px;
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 20;
        border-right: 1px solid #dad9f7;
        border-radius: 8px 0 0 8px;
        background-color: #dad9f7;
        top: 0;

        &>svg {
          display: inline-block;
        }
      }
    }
  }
}

.conditionSettingEdit {
  background-color: transparent !important;
  padding-left: 0;
  padding-right: 0;
  width: 100%;
}

.conditionsContainerEdit {
  width: 100% !important;
  background: #fff;
  border: 1px solid #DAD9F7;
}

.edit {
  width: 100% !important;
}

.sectionWrapperTitleEdit {
  font-family: 'Basis Grotesque Pro';
  font-style: normal;
  font-weight: 500;
  font-size: 18px;
  line-height: 24px;
  font-feature-settings: 'zero' on;
  color: #6a6d8f;
  margin-bottom: 16px;
}

.amount div {
  width: 160px !important;
}

.select-error {
  color: #cc2d14;
  font-family: "Basis Grotesque Pro";
  font-style: normal;
  font-weight: 400;
  font-size: 12px;
  line-height: 16px;
  margin-top: 8px;
  letter-spacing: 0.014em;
  font-feature-settings: "tnum" on, "lnum" on, "zero" on;
}
</style>
