# JsonC11

## 编码示例
``` c++
static string pingResult(InfoPtr & info)
		{
			json11::Json tm1 =
				json11::Json::object{
					{"seconds", info->timer.getStop_sec() },
					{"nanos", info->timer.getStop_nano() },
			};

			int state = 0;
			if (info->recvTimes == 0)
			{
				state = 1;
			}

			json11::Json res = json11::Json::object{
				{"realHost", info->cname },
				{"ipAddress",info->ipv4[0] },
				{"avgResponseTime", info->avgMs },
				{"maxResponseTime", info->maxMs },
				{"minResponseTime", info->minMs },
				{"sentTimes", info->times },
				{"receivedTimes", info->recvTimes },
				{"avgTtl", info->ttl },
				{"state", state},
				//{"aJob", {}},
				{"agentType", "Ping"},
				{"testStamp", tm1},
				{"storingStatus", 0 },
				{"retryTimes", info->times },

				{"populationId", info->com.populationId },
				{"nodeId", info->com.nodeId },
				{"monitorId", info->com.monitorId },
				{"jobId", info->com.jobId},
				{"accountId", info->com.accountId },

				{"testTime", Utils::getmSecNow() },
				{"expectedTime", info->com.expectedTime },
				
				{"version", "2.0.0.1" }


			};

			return res.dump();
		}
   ```
    
    ## 解码示例
  ``` c++
    static InfoPtr parseCurl(string & strJson)
		{
			string err;
			auto doc = json11::Json::parse(strJson, err);
			if (doc.is_null())
				return nullptr;

			auto item = doc["url"];
			if (item.is_null())
				return nullptr;
			// 解析URL
			InfoPtr info = std::make_shared<Info>(item.string_value());

			item = doc["timeout"];
			if (!item.is_null())
				info->timeouts = (int)item.int64_value();
			else
				info->timeouts = 5000;

			item = doc["name"];
			if (item.is_string())	
				info->jobName = item.string_value();
			else
				info->jobName = "unknow";
			

			
			info->type = TaskType::CURL;
			return info;
		}
  ```
    
