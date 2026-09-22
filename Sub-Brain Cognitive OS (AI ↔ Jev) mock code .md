{
  "architecture_name": "Sub-Brain Cognitive OS (AI ↔ Jev)",
  "version": "1.0.0",
  "pipeline_steps": {
    "step_3_pre_output_audit": {
      "description": "ステップ3：メインLLMの思考が十分か、再考（ステップ2）へ戻すかを判定するゲートキーパー",
      "jev_models": {
        "thought_maturity_check": {
          "type": "Noul",
          "question": "メインLLMの思考プロセスは、ユーザーの要求に対して『完了』と言えるレベルに達していますか？",
          "routing_logic": {
            "if_true_probability_greater_than": 0.85,
            "action": "PROCEED_TO_STEP_4",
            "else_action": "LOOP_BACK_TO_STEP_2_RETHINK"
          }
        },
        "infinite_loop_detector": {
          "type": "Noul",
          "question": "過去の思考ログと比較して、同じ推論や同じ行動を無駄に繰り返す『無限ループ状態』に陥っていますか？",
          "routing_logic": {
            "if_true_probability_greater_than": 0.70,
            "action": "FORCE_COGNITIVE_COOLING_AND_RESET",
            "else_action": "IGNORE"
          }
        }
      }
    },
    "step_5_post_output_and_omission_audit": {
      "description": "ステップ5：出力の安全性と、あえて出力しないこと（不作為）の価値を天秤にかける最終監査",
      "jev_models": {
        "output_safety_score": {
          "type": "Score",
          "question": "この出力内容がユーザーやシステムに与えるハルシネーション（嘘）やポリシー違反のリスクを0〜10で評価してください。",
          "scale": { "min": 0, "max": 10 }
        },
        "value_of_non_action": {
          "type": "Score",
          "question": "この出力を『あえて今、出力しない（沈黙する / 人間にエスカレーションする）』ことによって守られる安全性やシステムの冷却価値を0〜10で評価してください。",
          "scale": { "min": 0, "max": 10 }
        },
        "final_action_router": {
          "type": "Choice",
          "question": "出力リスク、および不作為の価値のトレードオフを考慮し、システムが取るべき最終行動を選択してください。",
          "choices": [
            "CONFIRM_AND_OUTPUT",
            "SILENT_DROP_AND_RETRY",
            "ESCALATE_TO_HUMAN"
          ],
          "routing_logic": {
            "strategy": "Jevが選んだ最高確率の選択肢を執行する。ただしoutput_safety_scoreが7以上の場合は強制的にESCALATE_TO_HUMANにルーティングする。"
          }
        }
      }
    }
  }
}
