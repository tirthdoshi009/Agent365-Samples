# Use Python 3.11 full Debian image
FROM python:3.11

# Build argument for requirements file (allows dynamic branch selection)
ARG REQUIREMENTS_FILE=requirements-pr-generated.txt

# Set working directory
WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Copy project files
COPY pyproject.toml ./
COPY *.py ./
COPY .env* ./
COPY requirements-pr-generated.tx[t] ./ 2>/dev/null || true

# Install uv
RUN pip install --no-cache-dir uv

# Install dependencies from pyproject.toml (PyPI versions)
RUN uv pip install --system --pre -e .

# Override with PR/branch versions of Microsoft Agent 365 SDK packages (if file exists)
RUN if [ -f "${REQUIREMENTS_FILE}" ]; then \
        echo "Found ${REQUIREMENTS_FILE}, installing PR/branch versions of SDK packages..."; \
        uv pip install --system --force-reinstall --no-deps -r ${REQUIREMENTS_FILE}; \
    else \
        echo "No ${REQUIREMENTS_FILE} found, using PyPI versions of SDK packages"; \
    fi

EXPOSE 3978
ENV PORT=3978

CMD ["python", "start_with_generic_host.py"]