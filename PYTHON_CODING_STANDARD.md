# PYTHON CODING STANDARD

## Overview

This document establishes comprehensive Python coding standards for projects, aligned with the latest Python best practices (Python 3.11+ features). These standards emphasize **explicit behavior**, **robust error handling**, **maintainable code structure**, and **reliability focus**, while incorporating core design principles such as simplicity, modularity, correctness, and consistency. The standards focus on clarity, reduced redundancy, and integration of modern guidelines for readability, security, and performance. All code must adhere to these rules to ensure robustness, testability, and ease of maintenance.

## Core Principles

Integrate foundational design principles for simplicity and explicitness:

- **Explicit Over Implicit**: Make intentions clear through code structure and naming. Avoid hidden behaviors and magic constants. Prefer verbose clarity over clever brevity.
- **Composition Over Inheritance**: Use composition and interfaces for code reuse. Design small, focused classes. Favor composition for extending behavior.
- **Error Handling Excellence**: Handle every error explicitly. Provide contextual error information. Use exception chaining to preserve call stack.
- **Simplicity and Clarity**: Strive for the simplest solution that meets requirements. Choose clear names and straightforward control flow over clever constructs. Avoid premature optimization.
- **Modularity and Abstraction**: Isolate responsibilities into small, composable units. Define clear boundaries and interfaces.
- **Correctness and Testing**: Treat tests as first-class citizens. Write tests before or alongside code. Validate inputs at boundaries.
- **Maintainability and Readability**: Keep functions short and focused. Remove dead code immediately. Follow consistent conventions.
- **Performance with Purpose**: Measure before optimizing. Reduce allocations in hot paths.
- **Consistency and Convention**: Follow established style guides rigorously.
- **Security and Safety**: Design for secure defaults. Validate and sanitize inputs.
- **Self-Documenting Code**: Write code that explains itself. Clear comments explain "why", not "what". Going over the code should give a clear picture.

## Module and Package Organization

### Package Structure

- Use lowercase, short, descriptive package names.
- Avoid generic names like `util` or `common`.
- One package per directory.
- Use `__init__.py` files to define public APIs.

**Example**:

```python
# Good
from audio import playback
from tts import engine
from config import settings

# Bad
from utils import helpers  # Too generic
```

### Import Organization

- Group imports: standard library first, third-party second, local last.
- Separate groups with blank lines.
- Use absolute imports for clarity.

**Example**:

```python
import os
import sys
import time
from pathlib import Path
from typing import Dict, List, Optional, Union

import numpy as np
import torch
import torchaudio

from .config import AudioConfig
from .utils import format_duration
```

## Variable and Constant Declaration

### Naming Conventions

- Use snake_case for variables and functions.
- Use PascalCase for classes.
- Use UPPER_CASE for constants.
- Add context to names in large scopes.

**Example**:

```python
# Constants
DEFAULT_TIMEOUT_SECONDS = 300
DEFAULT_GPU_MEMORY_LIMIT_GB = 5.5
MIN_WORKERS = 1
MAX_WORKERS = 32

# Classes
class AudioProcessor:
    def __init__(self, config: AudioConfig):
        self.config = config
        self._initialized = False
        self._lock = threading.RLock()

# Variables
def process_audio_chunks(chunks: List[str], output_dir: Path) -> None:
    total_chunks = len(chunks)
    processed_count = 0
    
    for chunk in chunks:
        if process_single_chunk(chunk, output_dir):
            processed_count += 1
```

### Variable Scope and Declaration

- Declare variables close to usage.
- Use descriptive names in all scopes.
- Initialize explicitly.

**Example**:

```python
def process_batch(items: List[Item]) -> None:
    batch_size = 100
    total_items = len(items)
    processed_count = 0

    for i in range(0, total_items, batch_size):
        end_index = min(i + batch_size, total_items)
        batch = items[i:end_index]
        
        if process_batch(batch):
            processed_count += len(batch)
```

## Function and Method Design

### Function Signatures

- Keep parameter lists short.
- Use type hints for all parameters and return values.
- Return errors as exceptions, not error codes.
- Use dataclasses for complex parameter groups.

**Example**:

```python
from dataclasses import dataclass
from typing import Optional, List

@dataclass
class ProcessingConfig:
    timeout_seconds: int = 300
    gpu_memory_limit_gb: float = 5.5
    workers: int = 4

def process_audio_file(
    file_path: Path,
    config: ProcessingConfig,
    output_dir: Optional[Path] = None
) -> Path:
    """Process an audio file with the given configuration.
    
    Args:
        file_path: Path to the input audio file
        config: Processing configuration
        output_dir: Optional output directory (defaults to input directory)
        
    Returns:
        Path to the processed output file
        
    Raises:
        FileNotFoundError: If input file doesn't exist
        ProcessingError: If audio processing fails
    """
    if not file_path.exists():
        raise FileNotFoundError(f"Audio file not found: {file_path}")
    
    if output_dir is None:
        output_dir = file_path.parent
    
    return _process_audio_internal(file_path, config, output_dir)
```

### Method Design

- Use `self` for instance methods.
- Use `@classmethod` for class methods.
- Use `@staticmethod` for utility functions.
- Be consistent within a class.

**Example**:

```python
class AudioEngine:
    def __init__(self, config: AudioConfig):
        self.config = config
        self._initialized = False
        self._lock = threading.RLock()
    
    def initialize(self) -> None:
        """Initialize the audio engine."""
        with self._lock:
            if self._initialized:
                return
            
            self._setup_audio_system()
            self._initialized = True
    
    @classmethod
    def create_default(cls) -> 'AudioEngine':
        """Create an AudioEngine with default configuration."""
        config = AudioConfig()
        return cls(config)
    
    @staticmethod
    def validate_config(config: AudioConfig) -> None:
        """Validate audio configuration."""
        if config.sample_rate <= 0:
            raise ValueError("Sample rate must be positive")
```

## Error Handling Patterns

### Exception Types and Chaining

- Create custom exception types for domains.
- Use exception chaining for context.
- Implement proper exception hierarchies.

**Example**:

```python
class AudioProcessingError(Exception):
    """Base exception for audio processing errors."""
    pass

class AudioFormatError(AudioProcessingError):
    """Raised when audio format is not supported."""
    pass

class AudioQualityError(AudioProcessingError):
    """Raised when audio quality requirements are not met."""
    pass

def process_audio_file(file_path: Path) -> None:
    try:
        audio = load_audio_file(file_path)
    except UnsupportedFormatError as e:
        raise AudioFormatError(f"Unsupported format for {file_path}") from e
    except Exception as e:
        raise AudioProcessingError(f"Failed to process {file_path}") from e
```

### Error Checking Patterns

- Use exceptions for error handling.
- Validate inputs at function boundaries.
- Provide clear error messages.

**Example**:

```python
def validate_audio_config(config: AudioConfig) -> None:
    """Validate audio configuration parameters."""
    if config.sample_rate <= 0:
        raise ValueError(f"Sample rate must be positive, got {config.sample_rate}")
    
    if config.bit_depth not in (16, 24, 32):
        raise ValueError(f"Bit depth must be 16, 24, or 32, got {config.bit_depth}")
    
    if config.channels not in (1, 2):
        raise ValueError(f"Channels must be 1 or 2, got {config.channels}")

def process_audio_with_validation(file_path: Path, config: AudioConfig) -> None:
    """Process audio file with configuration validation."""
    validate_audio_config(config)
    
    if not file_path.exists():
        raise FileNotFoundError(f"Audio file not found: {file_path}")
    
    # Process audio...
```

## Class and Interface Design

### Class Design

- Keep classes small and focused.
- Use composition over inheritance.
- Include proper type hints.

**Example**:

```python
from abc import ABC, abstractmethod
from typing import Protocol

class AudioProcessor(Protocol):
    """Protocol for audio processors."""
    
    def process(self, audio_data: bytes) -> bytes:
        """Process audio data."""
        ...

class BaseAudioProcessor:
    """Base class for audio processors."""
    
    def __init__(self, config: AudioConfig):
        self.config = config
        self._initialized = False
    
    def initialize(self) -> None:
        """Initialize the processor."""
        if not self._initialized:
            self._setup_processor()
            self._initialized = True

class HighQualityAudioProcessor(BaseAudioProcessor):
    """High-quality audio processor implementation."""
    
    def process(self, audio_data: bytes) -> bytes:
        """Process audio data with high quality settings."""
        self.initialize()
        return self._process_high_quality(audio_data)
    
    def _process_high_quality(self, audio_data: bytes) -> bytes:
        """Internal high-quality processing."""
        # Implementation...
        pass
```

## Concurrency Patterns

### Threading and Async

- Use `threading.Lock` for thread safety.
- Use `asyncio` for I/O-bound operations.
- Use `concurrent.futures` for CPU-bound operations.

**Example**:

```python
import threading
import asyncio
from concurrent.futures import ThreadPoolExecutor
from typing import List

class ThreadSafeAudioProcessor:
    def __init__(self):
        self._lock = threading.RLock()
        self._processing = False
    
    def process_audio(self, audio_data: bytes) -> bytes:
        """Thread-safe audio processing."""
        with self._lock:
            if self._processing:
                raise RuntimeError("Already processing audio")
            
            self._processing = True
            try:
                return self._process_internal(audio_data)
            finally:
                self._processing = False

async def process_audio_files_async(files: List[Path]) -> List[bytes]:
    """Process multiple audio files asynchronously."""
    async def process_single_file(file_path: Path) -> bytes:
        return await asyncio.to_thread(process_audio_file, file_path)
    
    tasks = [process_single_file(file) for file in files]
    return await asyncio.gather(*tasks)

def process_audio_files_parallel(files: List[Path], max_workers: int = 4) -> List[bytes]:
    """Process multiple audio files in parallel."""
    with ThreadPoolExecutor(max_workers=max_workers) as executor:
        return list(executor.map(process_audio_file, files))
```

## Testing Standards

### Unit Testing

- Use pytest for testing.
- Use descriptive test names.
- Test both success and failure cases.

**Example**:

```python
import pytest
from pathlib import Path
from unittest.mock import Mock, patch

class TestAudioProcessor:
    def test_process_audio_success(self):
        """Test successful audio processing."""
        processor = AudioProcessor(AudioConfig())
        audio_data = b"test_audio_data"
        
        result = processor.process(audio_data)
        
        assert result is not None
        assert len(result) > 0
    
    def test_process_audio_invalid_config(self):
        """Test audio processing with invalid configuration."""
        invalid_config = AudioConfig(sample_rate=-1)
        
        with pytest.raises(ValueError, match="Sample rate must be positive"):
            AudioProcessor(invalid_config)
    
    @patch('audio.processor.load_audio_file')
    def test_process_audio_file_not_found(self, mock_load):
        """Test processing non-existent audio file."""
        mock_load.side_effect = FileNotFoundError("File not found")
        
        with pytest.raises(AudioProcessingError):
            process_audio_file(Path("nonexistent.wav"))
```

### Integration Testing

- Test complete workflows.
- Use temporary files and directories.
- Clean up after tests.

**Example**:

```python
import tempfile
import shutil
from pathlib import Path

class TestAudioWorkflow:
    def test_complete_audio_workflow(self, tmp_path: Path):
        """Test complete audio processing workflow."""
        # Setup
        input_file = tmp_path / "input.wav"
        output_dir = tmp_path / "output"
        output_dir.mkdir()
        
        # Create test audio file
        create_test_audio_file(input_file)
        
        # Process
        config = AudioConfig(sample_rate=96000, bit_depth=32)
        processor = AudioProcessor(config)
        
        result_file = processor.process_file(input_file, output_dir)
        
        # Verify
        assert result_file.exists()
        assert result_file.suffix == ".wav"
        
        # Cleanup handled by pytest tmp_path fixture
```

## Code Organization

### File Organization

- One main class per file.
- Group related functions.
- Use `__all__` to define public API.

**Example**:

```python
"""
Audio processing module for high-quality audio operations.

This module provides audio processing capabilities with support for
multiple formats and quality settings.
"""

from pathlib import Path
from typing import Optional, List

from .config import AudioConfig
from .exceptions import AudioProcessingError
from .utils import format_duration

__all__ = [
    'AudioProcessor',
    'process_audio_file',
    'AudioConfig',
    'AudioProcessingError',
]

class AudioProcessor:
    """High-quality audio processor."""
    # Implementation...

def process_audio_file(file_path: Path, config: AudioConfig) -> Path:
    """Process an audio file with the given configuration."""
    # Implementation...
```

## Performance Guidelines

### Memory Management

- Use generators for large datasets.
- Use `__slots__` for memory-intensive classes.
- Profile before optimizing.

**Example**:

```python
class AudioChunk:
    """Memory-efficient audio chunk representation."""
    __slots__ = ['data', 'sample_rate', 'channels']
    
    def __init__(self, data: bytes, sample_rate: int, channels: int):
        self.data = data
        self.sample_rate = sample_rate
        self.channels = channels

def process_audio_chunks(chunk_size: int = 1024):
    """Process audio in chunks to reduce memory usage."""
    for chunk in generate_audio_chunks(chunk_size):
        yield process_chunk(chunk)
```

### String Operations

- Use f-strings for string formatting.
- Use `join()` for concatenating lists.

**Example**:

```python
# Good
message = f"Processed {count} files in {duration:.2f} seconds"
file_path = Path("/path") / "to" / "file.wav"

# Bad
message = "Processed " + str(count) + " files in " + str(duration) + " seconds"
file_path = "/path/to/file.wav"
```

## Documentation Standards

### Module Documentation

- Explain purpose and usage.
- Provide examples.

**Example**:

```python
"""
Audio processing module for high-quality audio operations.

This module provides comprehensive audio processing capabilities with support
for multiple formats (WAV, MP3, FLAC, OGG) and quality settings up to 96kHz/32-bit.

Usage:
    from audio import AudioProcessor, AudioConfig
    
    config = AudioConfig(sample_rate=96000, bit_depth=32)
    processor = AudioProcessor(config)
    
    result = processor.process_file("input.wav", "output/")
"""

# Module implementation...
```

### Function Documentation

- Use docstring format.
- Include parameters, returns, examples.

**Example**:

```python
def process_audio_with_quality(
    file_path: Path,
    quality: AudioQuality,
    normalize: bool = True
) -> bytes:
    """Process audio file with specified quality settings.
    
    This function loads an audio file and processes it according to the
    specified quality settings, optionally applying normalization.
    
    Args:
        file_path: Path to the input audio file
        quality: Audio quality configuration
        normalize: Whether to normalize the audio (default: True)
        
    Returns:
        Processed audio data as bytes
        
    Raises:
        FileNotFoundError: If the input file doesn't exist
        AudioFormatError: If the audio format is not supported
        AudioQualityError: If quality requirements cannot be met
        
    Example:
        >>> quality = AudioQuality(sample_rate=96000, bit_depth=32)
        >>> data = process_audio_with_quality("input.wav", quality)
        >>> len(data) > 0
        True
    """
    # Implementation...
```

## Security Guidelines

### Input Validation

- Validate at boundaries.
- Use allowlists.

**Example**:

```python
ALLOWED_AUDIO_FORMATS = {'.wav', '.mp3', '.flac', '.ogg'}

def validate_audio_file(file_path: Path) -> None:
    """Validate audio file path and format."""
    if not file_path.exists():
        raise FileNotFoundError(f"Audio file not found: {file_path}")
    
    if file_path.suffix.lower() not in ALLOWED_AUDIO_FORMATS:
        raise AudioFormatError(f"Unsupported format: {file_path.suffix}")
    
    if file_path.stat().st_size == 0:
        raise ValueError(f"Audio file is empty: {file_path}")

def process_audio_file(file_path: Path) -> bytes:
    """Process audio file with validation."""
    validate_audio_file(file_path)
    # Process file...
```

### Secret Management

- Use environment variables.
- Never log secrets.

**Example**:

```python
import os
from typing import Optional

def get_api_key() -> Optional[str]:
    """Get API key from environment variable."""
    api_key = os.getenv("AUDIO_API_KEY")
    if not api_key:
        raise ValueError("AUDIO_API_KEY environment variable not set")
    return api_key

def log_processing_info(file_path: Path, duration: float) -> None:
    """Log processing information (no secrets)."""
    logger.info(f"Processed {file_path} in {duration:.2f} seconds")
```

## Compliance Checklist

- [ ] Functions have descriptive names and type hints.
- [ ] Exceptions handled explicitly with proper chaining.
- [ ] Classes small and focused with clear interfaces.
- [ ] Tests cover all cases including edge cases.
- [ ] Documentation up-to-date with examples.
- [ ] Inputs validated at function boundaries.
- [ ] No hardcoded values or magic numbers.
- [ ] Code passes flake8, mypy, and black formatting.
- [ ] Self-documenting code with clear comments explaining "why".
- [ ] Going over the code gives a clear picture of functionality.
- [ ] Error messages are descriptive and actionable.
- [ ] No unused imports or dead code.
- [ ] Consistent naming conventions throughout.
- [ ] Proper exception hierarchies for domain-specific errors.
- [ ] Thread-safe implementations where needed.
- [ ] Performance considerations for hot paths.
- [ ] Security best practices for input validation.
- [ ] Environment-based configuration management.
