# How to implement my own VisionRetriever?

- Instantiate a class inherited from `VisionRetriever` abstract class.
- Implement `forward_query`, `forward_documents` and `get_scores` abstract methods.
- [OPTIONAL] Implement `get_relevant_docs_results` and `compute_metrics` if you want custom implementation for metric computation.

If you want to call directly your class by your model_name (e.g. in  `evaluate.py` script) you can do the following:

- Add decorator `@register_vision_retriever([your model name])`
- Import your class to the `vidore_benchmark/retrievers/__init__.py` file

Examples be found in `src/vidore_benchmark/retrievers/` :

```python
#src/vidore_benchmark/retrievers/dummy_retriever.py
from vidore_benchmark.retrievers.vision_retriever import VisionRetriever
from vidore_benchmark.retrievers.utils.register_models import register_text_retriever
from typing import List
from tqdm import tqdm
from PIL import Image
import torch

@register_vision_retriever("dummy_retriever")
class DummyRetriever(VisionRetriever):
    def __init__(
        self,
        emb_dim_query: int = 512,
        emb_dim_doc: int = 512,
    ):
        super().__init__()
        self.emb_dim_query = emb_dim_query
        self.emb_dim_doc = emb_dim_doc

    @property
    def use_visual_embedding(self) -> bool:
        return False

    def forward_queries(self, queries: List[str], **kwargs) -> torch.Tensor:
        return torch.randn(len(queries), self.emb_dim_query)

    def forward_documents(self, documents: List[Image.Image], **kwargs) -> torch.Tensor:
        return torch.randn(len(documents), self.emb_dim_doc)

    def get_scores(
        self,
        queries: List[str],
        documents: List[Image.Image | str],
        batch_query: int,
        batch_doc: int,
        **kwargs,
    ) -> torch.Tensor:
        scores = torch.randn(len(queries), len(documents))
        return scores
```