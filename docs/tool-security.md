# Tool Security

The agent is designed around least privilege.

## Allowed interfaces

```text
fs.read(path)
fs.write(path, content)
fs.list(path)
py.run(script_path)
gpu.status()
resource.search(query)
resource.read(doc_id, page)
db.query(sql)
```

## Excluded interfaces

```text
bash
pip install
git
network access
vllm-env modification
served-model modification
```

Python execution is intended to run inside a controlled run directory. The initial execution mode is CPU-only so it does not compete with vLLM for GPU resources.

Database access should remain read-only for the agent unless a future operation is explicitly authorized.
