<template>
  <div class="p-10 m-4 rounded-md bg-white">
    <Alert message="刷新后会还原" show-icon />

    <CurrentPermissionMode />

    <p>
      当前拥有的code列表: <a> {{ permissionStore.getPermCodeListState }} </a>
    </p>
    <Divider />
    <Alert class="mt-4" type="info" message="点击后请查看按钮变化" show-icon />
    <Divider />
    <a-button type="primary" class="mr-2" @click="changePermissionCode('2')">
      点击切换按钮权限(用户id为2)
    </a-button>
    <a-button type="primary" @click="changePermissionCode('1')">
      点击切换按钮权限(用户id为1,默认)
    </a-button>

    <Divider>组件方式判断权限</Divider>
    <Authority :value="'1000'">
      <a-button type="primary" class="mx-4">拥有code ['1000']权限可见</a-button>
    </Authority>

    <Authority :value="'2000'">
      <a-button color="success" class="mx-4">拥有code ['2000']权限可见</a-button>
    </Authority>

    <Authority :value="['1000', '2000']">
      <a-button color="error" class="mx-4">拥有code ['1000','2000']角色权限可见</a-button>
    </Authority>

    <Divider>函数方式方式判断权限</Divider>
    <a-button v-if="hasPermission('1000')" type="primary" class="mx-4">
      拥有code ['1000']权限可见
    </a-button>

    <a-button v-if="hasPermission('2000')" color="success" class="mx-4">
      拥有code ['2000']权限可见
    </a-button>

    <a-button v-if="hasPermission(['1000', '2000'])" color="error" class="mx-4">
      拥有code ['1000','2000']角色权限可见
    </a-button>

    <Divider>指令方式方式判断权限(该方式不能动态修改权限.)</Divider>
    <a-button v-auth="'1000'" type="primary" class="mx-4"> 拥有code ['1000']权限可见 </a-button>

    <a-button v-auth="'2000'" color="success" class="mx-4"> 拥有code ['2000']权限可见 </a-button>

    <a-button v-auth="['1000', '2000']" color="error" class="mx-4">
      拥有code ['1000','2000']角色权限可见
    </a-button>
  </div>
</template>
<script lang="ts">
  import { defineComponent } from 'vue';
  import { Alert, Divider } from 'ant-design-vue';
  import CurrentPermissionMode from '../CurrentPermissionMode.vue';
  import { usePermission } from '/@/hooks/web/usePermission';
  import Authority from '/@/components/Authority';
  import { getPermCodeByUserId } from '/@/api/sys/user';
  import { permissionStore } from '/@/store/modules/permission';
  import { PermissionModeEnum } from '/@/enums/appEnum';
  export default defineComponent({
    components: { Alert, CurrentPermissionMode, Divider, Authority },
    setup() {
      const { hasPermission } = usePermission();

      // !模拟从后台获取权限编码， 该函数可能只需要执行一次，实际项目可以自行放到合适的时机
      async function changePermissionCode(userId: string) {
        const codeList = await getPermCodeByUserId({ userId });
        permissionStore.commitPermCodeListState(codeList);
      }
      // 默认初始化为1
      changePermissionCode('1');
      return {
        hasPermission,
        permissionStore,
        changePermissionCode,
        PermissionModeEnum,
      };
    },
  });
</script>
