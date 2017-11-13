# Read me
** 目录 **
simulated_dialog.json: 存储由user simulator 和agent产生的对话，每个ID对应一个episode
result.json：存储从上文件的对话中提取出的四元组[s_t,a_t,s_t+1,r]
state_tracker.py: class StateTracker 用于get_state,和追踪对话历史
get_four.py: class GetFour 得到四元组[s_t,a_t,s_t+1,r]
# How to use class GetFour?
<if __name__ == '__main__':

    getstate = GetFour('simulated_dialog.json') # 传入对话act地址
    four = getstate.get_four()
    f = open('result.json', 'w', encoding='utf-8') # 保存结果的地址
    json.dump(four, f, ensure_ascii=False)
    f.close()
>
# Input Format
<{
    "0": [
        {
            "diaact": "inform",
            "request_slots": {},
            "inform_slots": {
                "user_goal": "预约"
            },
            "speaker": "user"
        },
        {
            "diaact": "request",
            "request_slots": {
                "child_age": "UNK"
            },
            "inform_slots": {},
            "speaker": "agent"
        }
    ]
}>
每个ID为一个episode，每个episode里的每一项为一个diaact
# Result
<{
    "0": [
        # state
        {
            "user_action": {
                "diaact": "inform",
                "request_slots": {},
                "inform_slots": {
                    "user_goal": "预约"
                },
                "speaker": "user",
                "turn": 0
            },
            "current_slots": {
                "inform_slots": {
                    "user_goal": "预约"
                },
                "request_slots": {},
                "proposed_slots": {},
                "agent_request_slots": {}
            },
            "turn": 1,
            "history": [
                {
                    "diaact": "inform",
                    "request_slots": {},
                    "inform_slots": {
                        "user_goal": "预约"
                    },
                    "speaker": "user",
                    "turn": 0
                }
            ],
            "agent_action": null
        },
        # agent_action
        {
            "diaact": "request",
            "request_slots": {
                "child_age": "UNK"
            },
            "inform_slots": {},
            "speaker": "agent"
        },
        # next state
        {
            "user_action": {
                "diaact": "inform",
                "request_slots": {},
                "inform_slots": {
                    "child_age": "1岁"
                },
                "speaker": "user",
                "turn": 2
            },
            "current_slots": {
                "inform_slots": {
                    "user_goal": "预约",
                    "child_age": "1岁"
                },
                "request_slots": {},
                "proposed_slots": {},
                "agent_request_slots": {
                    "child_age": "UNK"
                }
            },
            "turn": 3,
            "history": [
                {
                    "diaact": "inform",
                    "request_slots": {},
                    "inform_slots": {
                        "user_goal": "预约"
                    },
                    "speaker": "user",
                    "turn": 0
                },
                {
                    "diaact": "request",
                    "request_slots": {
                        "child_age": "UNK"
                    },
                    "inform_slots": {},
                    "speaker": "agent",
                    "turn": 1
                },
                {
                    "diaact": "inform",
                    "request_slots": {},
                    "inform_slots": {
                        "child_age": "1岁"
                    },
                    "speaker": "user",
                    "turn": 2
                }
            ],
            "agent_action": {
                "diaact": "request",
                "request_slots": {
                    "child_age": "UNK"
                },
                "inform_slots": {},
                "speaker": "agent",
                "turn": 1
            }
        },
        # reward
        -2
    ]
}>
每个ID对应一个四元组
每个episode的第一个四元组里的state的agent_action为null
** 对话状态的判断 **：每个episode结束时，最后一个state["current_slots"]["inform_slots"]里存在phone_number，即获得用户的电话号码即为成功。
** reward的制定规则 **：对话未完成则为-1，失败-num_turns,成功+2*num_turns

