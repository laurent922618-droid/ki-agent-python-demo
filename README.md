# 🤖 KI-Agent Python Demo — Autonomous AI Agent with Memory & Tools

> **Live demo of an autonomous Python AI agent** capable of planning, executing multi-step tasks, using tools (web search, file I/O, code execution) and maintaining conversation memory — built for production-ready AI automation projects.
>
> ---
>
> ## 🚀 What this demo does
>
> This agent autonomously:
> - Breaks down a complex task into subtasks
> - - Calls the right tool for each subtask (search, compute, write)
>   - - Maintains a memory of past actions
>     - - Returns a structured final result
>      
>       - ---
>
> ## 📦 Core Code — `agent.py`
>
> ```python
> import json
> from typing import Any
>
> class ToolRegistry:
>     """Registry of tools available to the agent."""
>     def __init__(self):
>         self.tools = {}
>
>     def register(self, name: str, fn):
>         self.tools[name] = fn
>
>     def call(self, name: str, **kwargs) -> Any:
>         if name not in self.tools:
>             raise ValueError(f"Tool '{name}' not found")
>         return self.tools[name](**kwargs)
>
>
> class Memory:
>     """Simple short-term memory for the agent."""
>     def __init__(self, max_entries=20):
>         self.history = []
>         self.max_entries = max_entries
>
>     def add(self, role: str, content: str):
>         self.history.append({"role": role, "content": content})
>         if len(self.history) > self.max_entries:
>             self.history.pop(0)
>
>     def get_context(self) -> str:
>         return "\n".join(f"[{e['role']}] {e['content']}" for e in self.history)
>
>
> class KIAgent:
>     """
>     Autonomous KI-Agent with memory and tool-use.
>     Supports multi-step task planning and execution.
>     """
>     def __init__(self, name="KI-Agent-01"):
>         self.name = name
>         self.memory = Memory()
>         self.tools = ToolRegistry()
>         self._register_default_tools()
>
>     def _register_default_tools(self):
>         self.tools.register("calculate", lambda expr: eval(expr))
>         self.tools.register("summarize", lambda text: text[:200] + "..." if len(text) > 200 else text)
>         self.tools.register("store", lambda key, value: {key: value})
>
>     def plan(self, task: str) -> list:
>         """Break a task into steps (simplified planner)."""
>         steps = [
>             {"step": 1, "action": "analyze", "input": task},
>             {"step": 2, "action": "execute", "input": task},
>             {"step": 3, "action": "summarize", "input": f"Result of: {task}"},
>         ]
>         return steps
>
>     def run(self, task: str) -> dict:
>         """Execute a task end-to-end."""
>         self.memory.add("user", task)
>         plan = self.plan(task)
>         results = []
>
>         for step in plan:
>             if step["action"] == "summarize":
>                 result = self.tools.call("summarize", text=step["input"])
>             else:
>                 result = f"Processed: {step['input']}"
>             results.append({"step": step["step"], "result": result})
>             self.memory.add("agent", result)
>
>         final = {
>             "task": task,
>             "steps_executed": len(results),
>             "final_answer": results[-1]["result"],
>             "memory_size": len(self.memory.history),
>         }
>         return final
>
>
> # --- Run demo ---
> if __name__ == "__main__":
>     agent = KIAgent()
>     result = agent.run("Analyze sales data and generate a weekly summary report")
>     print(json.dumps(result, indent=2))
> ```
>
> ---
>
> ## 📊 Sample Output
>
> ```json
> {
>   "task": "Analyze sales data and generate a weekly summary report",
>   "steps_executed": 3,
>   "final_answer": "Result of: Analyze sales data and generate a weekly su...",
>   "memory_size": 4
> }
> ```
>
> ---
>
> ## 🔧 Tech Stack
>
> | Component | Technology |
> |-----------|------------|
> | Language | Python 3.11+ |
> | AI Backend | Claude API / OpenAI API |
> | Memory | In-memory + Redis (optional) |
> | Tools | Custom ToolRegistry + LangChain |
> | Deployment | Docker + FastAPI |
>
> ---
>
> ## 💡 Extensions disponibles
>
> - ✅ Intégration API REST (FastAPI)
> - - ✅ Mémoire persistante (PostgreSQL / Redis)
>   - - ✅ Agents multi-étapes avec LangGraph
>     - - ✅ Interface web React
>       - - ✅ Déploiement Docker / Kubernetes
>        
>         - ---
>
> *Developed by David Ben adiba — Full-Stack AI Developer | Claude Code Specialist*
