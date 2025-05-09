---
title : "អំពី GuardDuty"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3. </b> "
---

**មាតិកា**
- [ប្រភពទិន្នន័យ](#ប្រភពទិន្នន័យ)
- [ការរកឃើញ](#ការរកឃើញ)

#### ប្រភពទិន្នន័យ
ចាប់តាំងពីត្រូវបានបើកដំណើរការនៅក្នុង AWS Region មួយ GuardDuty ធ្វើការវិភាគរាល់ទិន្នន័យដែលមកពី៖
1. VPC Flow Logs
2. CloudTrail Logs  
3. DNS Logs
   - កំណត់ហេតុ DNS មកពី DNS resolvers (កម្មសិទ្ធិរបស់ AWS) សម្រាប់ VPCs ហើយមិនអាចចូលប្រើដោយផ្ទាល់ពីអ្នកប្រើប្រាស់បានទេ។
   - ប្រសិនបើ DNS resolver ត្រូវបានកំណត់រចនាសម្ព័ន្ធដោយឯករាជ្យដោយអ្នក ឬដោយភាគីទីបី GuardDuty នឹងមិនអាចចូលប្រើ ដំណើរការ និងកំណត់ការគំរាមកំហែងពីប្រភពទិន្នន័យនេះបានទេ។

GuardDuty អាចចូលប្រើប្រភពទិន្នន័យទាំងអស់ (ដែលបានរៀបរាប់ខាងលើ) ទោះបីជាពួកវាមិនទាន់ត្រូវបានបើកដំណើរការពីមុនក៏ដោយ។

GuardDuty គឺជាសេវាកម្ម **Regional** ដូច្នេះដើម្បីតាមដានទិន្នន័យនៅក្នុង AWS Region មួយ អ្នកត្រូវតែបើកដំណើរការវានៅក្នុង AWS Region នោះ។

ទោះបីជាអ្នកមានធនធាន AWS តិច ឬច្រើន (ដូចជា VPCs ឬ IAM users) GuardDuty នឹងមិនប៉ះពាល់ដល់ធនធានណាមួយទេ ពីព្រោះដំណើរការទាំងអស់នឹងត្រូវបានអនុវត្តផ្ទៃក្នុងនៅក្នុងសេវាកម្ម GuardDuty។

ការគិតថ្លៃរបស់ GuardDuty គឺផ្អែកលើ៖
- ចំនួន CloudTrail events ដែលបានវិភាគ
- បរិមាណនៃ VPC flow logs (គិតជា **GB**)
- បរិមាណនៃ DNS logs (គិតជា **GB**)

#### ការរកឃើញ
GuardDuty នឹងសកម្មតាមដាន និងស្វែងរកសញ្ញាមិនប្រក្រតីដែលមកពី៖
- ប្រភពទិន្នន័យទាំង 3 (ដែលបានរៀបរាប់ខាងលើ)
- ពី EC2 instances 
- ពីធនធាន AWS IAM

អ្នកអាចចូលមើលព័ត៌មានលម្អិតនៃការរកឃើញដែលត្រូវបានរកឃើញដោយ GuardDuty នៅក្នុងផ្ទាំង **Findings**។ ការរកឃើញនីមួយៗនឹងត្រូវបានបែងចែកជាព័ត៌មានតូចៗតាមទម្រង់ដែលអនុញ្ញាតឱ្យយើងងាយស្រួលអាន និងដោះស្រាយហានិភ័យសន្តិសុខ។

```
ThreatPurpose : ResourceTypeAffected / ThreatFamilyName . ThreatFamilyVariant ! Artifact
```

សម្រាប់ករណីនិងឥរិយាបថដែលពិបាកទស្សន៍ទាយ ដើម្បីរៀនសូត្រនិងវិភាគភាពមិនប្រក្រតី ម៉ាស៊ីន Machine Learning របស់ GuardDuty នឹងត្រូវការរយៈពេលមូលដ្ឋានពី 7 ទៅ 14 ថ្ងៃ។

**ឧទាហរណ៍៖**
1. EC2 instance មួយចាប់ផ្តើមទំនាក់ទំនងជាមួយម៉ាស៊ីនមេពីចម្ងាយតាមរយៈ Port មិនប្រក្រតីមួយ។
2. IAM user មួយចាប់ផ្តើមផ្លាស់ប្តូរ Route Tables ខណៈពេលដែលមិនធ្លាប់បានធ្វើសកម្មភាពនេះពីមុនមក។

សម្រាប់ករណីខាងលើ រាល់ការស្វែងរកទាំងអស់នឹងត្រូវបានផ្អែកលើ Signatures ដូច្នេះពួកវានឹងត្រូវបានរកឃើញក្នុងរយៈពេល 10 នាទីបន្ទាប់ពី CloudFormation stack ត្រូវបានបញ្ចប់។ ក្នុងអំឡុងពេលនៃការរកឃើញការគំរាមកំហែង ភាពយឺតយ៉ាវនឹងមានតិច ឬច្រើនអាស្រ័យលើភាពញឹកញាប់នៃការលេចឡើងនៃព័ត៌មានពាក់ព័ន្ធ និងពេលវេលាសរុបដែល GuardDuty អាចចូលប្រើ និងវិភាគព័ត៌មានទាំងនោះនៅប្រភពទិន្នន័យជាក់លាក់។

នៅពេលមានការផ្លាស់ប្តូរណាមួយនៅក្នុងការរកឃើញ ផ្អែកលើការកំណត់រចនាសម្ព័ន្ធពី Amazon CloudWatch Events GuardDuty នឹងផ្ញើការជូនដំណឹងទៅអ្នកភ្លាមៗក្នុងរយៈពេល 5 នាទី។ រាល់ការផ្លាស់ប្តូរពាក់ព័ន្ធពីលើកបន្តបន្ទាប់នឹងមាន ID ដូចគ្នាជាមួយនឹងការរកឃើញដើម ហើយការជូនដំណឹងនឹងត្រូវបានផ្ញើរៀងរាល់ 6 ម៉ោងគិតចាប់ពីការផ្ញើលើកដំបូង នេះគឺដើម្បីដោះស្រាយបរិមាណការជូនដំណឹងច្រើនក្នុងការរកឃើញតែមួយទៅកាន់អ្នកប្រើប្រាស់។