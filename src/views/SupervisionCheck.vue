<template>
  <div class="pl20 pr20 pb100 reception" id="reception">
    <el-form label-position="right" label-width="160px" :gutter="20" :model="formData">
      <el-card class="mb20" style="position: sticky;top: 0px;background: #fff;z-index: 1999">
        <div class="pl20 pr20 clearfix">
          <span>抽查批次号：</span>
          <el-input class="w200 mr10" placeholder="请输入样品编号" v-model="queryParams.iCodeNum"></el-input>
          <el-button type="primary" :disabled="loading" :loading="loading" icon="el-icon-search" @click="getPmInsPectByCode">从省平台同步</el-button>
          <el-button type="warning" class="fr" @click="onSave">保存</el-button>
          <!-- <el-button type="success" class="fr mr10">新建</el-button> -->
          <!-- <el-button type="primary" class="fr" @click="syncFromPPlatform">从省平台同步</el-button> -->
        </div>
      </el-card>
      
      <el-card>
        <div slot="header" class="clearfix">
          <span class="text-bold text-primary"><i class="fa fa-tasks" aria-hidden="true"></i>任务信息</span>
        </div>
        <el-row :gutter="10" v-loading="loading">
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="委托单位">
              <el-input v-model="formData.SupervisePlanInfo['TASKSOURCE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="计划编号">
              <el-input v-model="formData.SupervisePlanInfo['PLANNO']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="任务类别">
              <el-input v-model="formData.SupervisePlanInfo['TASKCLASS']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="计划文件号">
              <el-input v-model="formData.SupervisePlanInfo['SUPERVISIONPLANNO']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="委托单位地址">
              <el-input></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="抽样领域">
              <el-select v-model="formData.PmPlanSubInfo['CHECK_TYPE']" placeholder="请选择">
                <el-option
                  v-for="(value, key) in samplingAreaOptions"
                  :key="key"
                  :label="value"
                  :value="key">
                </el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
      <el-card class="mt20">
        <div slot="header" class="clearfix">
          <span class="text-bold text-primary"><i class="fa fa-address-book" aria-hidden="true"></i>主检部门信息</span>
        </div>
        <el-row :gutter="10" v-loading="loading">
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="主检部门">
              <el-input v-model="formData.Division['DIVISIONNAME']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="实验室">
              <el-input v-model="formData.Department['DEPTNAME']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="检验单位">
              <el-input v-model="formData.Division['']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="检测地点">
              <el-input v-model="formData.Department['LOCATION']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="受理单号">
              <el-input></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="受理人">
              <el-input :value="userInfo.uRealName"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="受理日期">
              <el-date-picker
                v-model="nowDate"
                type="date"
                placeholder="选择日期时间">
              </el-date-picker>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
      <!-- 受检单位信息 -->
      <el-card class="mt20">
        <div slot="header" class="clearfix">
          <span class="text-bold text-primary"><i class="fa fa-address-book-o" aria-hidden="true"></i>受检单位信息</span>
        </div>
        <el-row :gutter="10" v-loading="loading">
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="受检单位名称">
              <el-input v-model="formData.PmCaryInfo['CARY_NAME']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="受检单位编号">
              <el-input v-model="formData.PmCaryInfo['CARY_CODE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="受检单位地址">
              <el-input v-model="formData.PmCaryInfo['CARY_ADDR']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="受检单位法人代表">
              <el-input v-model="formData.PmCaryInfo['CARY_PRESENT']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="受检单位联系人">
              <el-input v-model="formData.PmCaryInfo['CARY_LINKMAN']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="受检单位联系方式">
              <el-input v-model="formData.PmCaryInfo['CARY_TEL']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="受检单位邮编">
              <el-input v-model="formData.PmCaryInfo['CARY_ZIP_CODE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="传真">
              <el-input v-model="formData.PmCaryInfo['CARY_FAX']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="协议编号">
              <el-input v-model="formData.PmCaryInfo['XYBH']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="所抽产品上年销售">
              <el-input v-model="formData.PmCaryInfo['NXL']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="销售额数据">
              <el-input v-model="formData.PmCaryInfo['XSE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="区域类别">
              <el-input v-model="locationOptions[formData.PmCaryInfo['OPERATOR_LOCATION']]"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="行政区划(省)">
              <el-input v-model="formData.PmCaryInfo['CARY_PROVINCE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="行政区划(市)">
              <el-input v-model="formData.PmCaryInfo['CARY_CITY']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="行政区划(区)">
              <el-input v-model="formData.PmCaryInfo['CARY_COUNTY']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="营业执照">
              <el-input v-model="formData.PmCaryInfo['UNIFIED_SOCIAL_CREDIT_CODE']" placeholder="统一社会信用代码"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
      <!-- 生产企业信息 -->
      <el-card class="mt20">
        <div slot="header" class="clearfix">
          <span class="text-bold text-primary"><i class="fa fa-address-card" aria-hidden="true"></i>生产企业信息</span>
        </div>
        <el-row :gutter="10" v-loading="loading">
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="生产单位名称">
              <el-input v-model="formData.PmProduceUnit['PRO_NAME']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="生产单位编号">
              <el-input v-model="formData.PmProduceUnit['PRO_CODE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="生产单位地址">
              <el-input v-model="formData.PmProduceUnit['PRO_ADDR']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="生产单位联系人">
              <el-input v-model="formData.PmProduceUnit['PRO_LINKMAN']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="生产单位联系电话">
              <el-input v-model="formData.PmProduceUnit['PRO_TEL']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="生产单位法人代表">
              <el-input v-model="formData.PmProduceUnit['PRO_PRESENT']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="邮编编码">
              <el-input v-model="formData.PmProduceUnit['PRO_ZIP_CODE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="企业规模">
              <el-input v-model="formData.PmProduceUnit['PRO_SACLE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="企业人数">
              <el-input v-model="formData.PmProduceUnit['PRO_NUM']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="经济规模">
              <el-input v-model="formData.PmProduceUnit['ECO_INSIDE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="销售额">
              <el-input v-model="formData.PmProduceUnit['xse']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="营业执照">
              <el-input v-model="formData.PmProduceUnit['PRO_ORGAN_CODE']" placeholder="统一社会信用代码"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="行政区划(省)">
              <el-input v-model="formData.PmProduceUnit['PRO_PROVINCE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="行政区划(市)">
              <el-input v-model="formData.PmProduceUnit['PRO_CITY']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="行政区划(区)">
              <el-input v-model="formData.PmProduceUnit['PRO_COUNTY']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="管理体系认证">
              <el-radio-group v-model="formData.PmProduceUnit['PRO_QUALITY_PASS']">
                <el-radio :label="1">通过</el-radio>
                <el-radio :label="null">未通过</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="认证证书编号">
              <el-input v-model="formData.PmProduceUnit['PRO_CERT_CODE']"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
      <!-- 抽样单位信息 -->
      <el-card class="mt20">
        <div slot="header" class="clearfix">
          <span class="text-bold text-primary"><i class="fa fa-address-card-o" aria-hidden="true"></i>抽样单位信息</span>
        </div>
        <el-row :gutter="10" v-loading="loading">
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="抽样单位名称">
              <el-input v-model="formData.PmExecUintInfo['EXEC_NAME']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="抽样单位地址">
              <el-input v-model="formData.PmExecUintInfo['EXEC_ADDR']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="抽样单位邮编">
              <el-input v-model="formData.PmExecUintInfo['EXEC_ZIP_CODE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="抽样单位联系人">
              <el-input v-model="formData.PmExecUintInfo['EXEC_LINKMAN']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="抽样单位联系电话">
              <el-input v-model="formData.PmExecUintInfo['EXEC_TEL']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="计划文件号">
              <el-input v-model="formData.PmExecUintInfo['EXEC_FILE_NO']"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
      <!-- 产品信息 -->
      <el-card class="mt20">
        <div slot="header" class="clearfix">
          <span class="text-bold text-primary"><i class="fa fa-archive" aria-hidden="true"></i>产品信息</span>
        </div>
        <el-row :gutter="10" v-loading="loading">
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="受理单号">
              <el-input></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="主检组">
              <el-input v-model="formData.PmPlanSubInfo['CARY_ID']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="样品编号">
              <el-input v-model="formData.PmPlanSubInfo['GOODS_ID']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="监管类别">
              <el-input v-model="formData.PmPlanSubInfo['TASK_KIND']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="证书编号">
              <el-input v-model="formData.PmPlanSubInfo['GOODS_CERT_CODE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="产品名称">
              <el-input v-model="formData.PmPlanSubInfo['GOODS_NAME']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="规格型号">
              <el-input v-model="formData.PmPlanSubInfo['GOODS_SCALE']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="生产日期">
              <el-input v-model="formData.PmPlanSubInfo['GOODS_PRO_TIME_TEMP']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="生产批号">
              <el-input v-model="formData.PmPlanSubInfo['GOODS_PRO_LOT']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="标注注册商标">
              <el-input v-model="formData.PmPlanSubInfo['GOODS_BRAND']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="样品到达日期">
              <el-input></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="样品数量">
              <el-input v-model="formData.PmPlanSubInfo['GOODS_CHECK_NUM']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="其中备样数量">
              <el-input v-model="formData.PmPlanSubInfo['GOODS_COPY_NUM']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="备样封存地点">
              <el-input v-model="formData.PmPlanSubInfo['GOODS_COPY_LOCAL']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="样品进货/库存数">
              <el-input v-model="formData.PmPlanSubInfo['PRO_JH_KC']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="标注执行标准/技术文件">
              <el-input v-model="formData.PmPlanSubInfo['GOODS_STANDARD']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="产品标注等级">
              <el-input v-model="formData.PmPlanSubInfo['GOODS_LEVEL']"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="明示质量承诺">
              <el-input></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="要求完成日期">
              <el-date-picker
                type="date"
                placeholder="选择日期时间">
              </el-date-picker>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="下达日期">
              <el-date-picker
                v-model="nowDate"
                type="date"
                placeholder="选择日期时间">
              </el-date-picker>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="抽样日期">
              <el-date-picker
                v-model="formData.PmPlanSubInfo['GOODS_CHECK_TIME']"
                type="date"
                placeholder="选择日期时间">
              </el-date-picker>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="接样人">
              <el-input :value="userInfo.uRealName"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="检查封样人">
              <el-input :value="userInfo.uRealName"></el-input>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="24" :md="12" :lg="8" :xl="8">
            <el-form-item label="备注">
              <el-input v-model="formData.PmPlanSubInfo['REMARKS']"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
    </el-form>
    <!-- 从省平台同步 -->
    <el-dialog title="从省平台同步" width="500px" :visible.sync="dialogFormVisible">
      <el-form label-position="right" :model="form" label-width="80">
        <el-form-item label="抽查批次号">
          <el-input v-model="form.name" class="w300"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="text-center">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { getPmInsPectByCode, inspectAcceptance } from "../api/api";
import date from '../../util/date';
export default {
  data() {
    return {
      loading: false,
      dialogFormVisible: false,
      queryParams: {
        iCodeNum: 'A202003011010009', //201909042080009  A202003011010009
      },
      formData: {
        ID: '',
        LOT_NUM: '',
        PLAN_CODE: '',
        PmCaryInfo: {},
        PmEcPlatFormInfo: {},
        PmExecUintInfo: {},
        PmPlanInfo: {},
        PmPlanSubInfo: {},
        PmProduceUnit: {},
        SupervisePlanInfo: {},
        Department: {},
        Division: {}
      },
      form: {
        iCodeNum: ''
      },
      locationOptions: {
        1:'城区',
        2:'城郊结合部',
        3:'乡镇'
      },
      samplingAreaOptions: {
        '1': '现场抽样',
        '2': '市场买样',
        '3': '电商买样',
        '4': '其他来源'
      }
    };
  },
  computed: {
    userInfo() {
      return window.localStorage.getItem('user') && JSON.parse(window.localStorage.getItem('user'));
    },
    nowDate() {
      return date.getNowDate();
    }
  },
  methods: {
    // 从省平台同步
    getPmInsPectByCode() {
      this.loading = true;
      getPmInsPectByCode({iCodeNum: this.queryParams.iCodeNum})
        .then(res => {
          this.loading = false;
          if (res.data && res.data.response) {
            this.formData = res.data.response;
          } 
        })
        .catch(err => {
          this.loading = false;
        })
    },
    // 从省平台同步
    // syncFromPPlatform() {
    //   this.dialogFormVisible = true
    // },
    // syncFromPPlatformConfirm() {
    //   this.dialogFormVisible = false
    // },
    // 保存
    onSave() {
      this.$confirm('保存前请确定信息无误?', '温馨提醒', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning',
          beforeClose: (action, instance, done) => {
            if (action === 'confirm') {
              instance.confirmButtonLoading = true;
              instance.confirmButtonText = '执行中...';
              inspectAcceptance(this.formData)
                .then(res => {
                  done();
                  instance.confirmButtonLoading = false;
                  if (res) {
                    this.$message({
                      type: 'success',
                      message: '保存成功!'
                    });
                  }
                })
                .catch(err => {
                  instance.confirmButtonLoading = false;
                })
            } else {
              done();
            }
          }
        })
    }
  },
};
</script>

<style lang='scss' scoped>
  .reception {
    position: relative;
  }
</style>
