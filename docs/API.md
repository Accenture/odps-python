# ODPS Python Library API Reference

## Table of Contents

1. [Core Classes](#core-classes)
2. [Data Models](#data-models)
3. [Validation Framework](#validation-framework)
4. [Exception Hierarchy](#exception-hierarchy)
5. [Type Protocols](#type-protocols)
6. [Enumerations](#enumerations)
7. [Utility Functions](#utility-functions)

---

## Core Classes

### OpenDataProduct

The main class for handling ODPS v4.0 documents.

```python
class OpenDataProduct:
    """Main class for handling Open Data Product Specification documents."""
```

#### Constructor

```python
def __init__(self, product_details: ProductDetails) -> None:
    """Initialize with mandatory product details."""
```

**Parameters:**
- `product_details` (ProductDetails): Core product information (required)

#### Class Methods

##### from_dict
```python
@classmethod
def from_dict(cls, data: Dict[str, Any]) -> 'OpenDataProduct':
    """Create OpenDataProduct from dictionary."""
```

##### from_json
```python
@classmethod
def from_json(cls, json_str: str) -> 'OpenDataProduct':
    """Load from JSON string."""
```

##### from_yaml
```python
@classmethod
def from_yaml(cls, yaml_str: str) -> 'OpenDataProduct':
    """Load from YAML string."""
```

##### from_file
```python
@classmethod
def from_file(cls, file_path: Union[str, Path]) -> 'OpenDataProduct':
    """Load from file (JSON or YAML)."""
```

#### Instance Methods

##### validate
```python
def validate(self) -> bool:
    """Validate the ODPS document using the validation framework."""
```

**Returns:** `bool` - True if valid

**Raises:** `ODPSValidationError` - If validation fails

##### to_dict
```python
def to_dict(self) -> Dict[str, Any]:
    """Convert to dictionary representation."""
```

##### to_json
```python
def to_json(self, indent: int = 2) -> str:
    """Convert to JSON string with caching."""
```

##### to_yaml
```python
def to_yaml(self) -> str:
    """Convert to YAML string with caching."""
```

##### save
```python
def save(self, file_path: Union[str, Path], format: str = 'auto') -> None:
    """Save to file."""
```

**Parameters:**
- `file_path` (str | Path): Path to save file
- `format` (str): Format to use ('json', 'yaml', or 'auto')

#### Component Management Methods

##### add_data_contract
```python
def add_data_contract(self, url: Optional[str] = None, 
                     specification: Optional[Dict[str, Any]] = None) -> None:
    """Add or update data contract."""
```

##### add_sla
```python
def add_sla(self, url: Optional[str] = None,
            specification: Optional[Dict[str, Any]] = None) -> None:
    """Add or update SLA."""
```

##### add_license
```python
def add_license(self, scope_of_use: str, **kwargs) -> None:
    """Add or update license."""
```

##### add_data_access
```python
def add_data_access(self, default_method: DataAccessMethod, 
                   **additional_methods) -> None:
    """Add or update data access methods."""
```

#### Properties

##### is_valid
```python
@property
def is_valid(self) -> bool:
    """Check if document is valid without raising exceptions."""
```

##### validation_errors
```python
@property
def validation_errors(self) -> List[str]:
    """Get list of validation errors without raising exceptions."""
```

##### has_optional_components
```python
@property
def has_optional_components(self) -> bool:
    """Check if document has any optional components."""
```

##### component_count
```python
@property
def component_count(self) -> int:
    """Count of non-None optional components."""
```

##### is_production_ready
```python
@property
def is_production_ready(self) -> bool:
    """Check if document is ready for production."""
```

##### compliance_level
```python
@property
def compliance_level(self) -> str:
    """Get compliance level based on components present."""
```

**Returns:** One of: `"invalid"`, `"minimal"`, `"basic"`, `"substantial"`, `"full"`

#### Performance Methods

##### check_component_protocols
```python
def check_component_protocols(self) -> Dict[str, bool]:
    """Check if all components follow their respective protocols."""
```

---

## Data Models

All data models are implemented as dataclasses with comprehensive type hints.

### ProductDetails

Core product information (required).

```python
@dataclass
class ProductDetails:
    """Product information and metadata."""
    
    # Required fields
    name: str
    product_id: str
    visibility: str  # private, invitation, organisation, dataspace, public
    status: str      # draft, development, production, deprecated, etc.
    type: str        # dataset, algorithm, ml-model, etc.
    
    # Optional fields
    value_proposition: Optional[str] = None
    description: Optional[str] = None
    categories: List[str] = field(default_factory=list)
    tags: List[str] = field(default_factory=list)
    brand: Optional[str] = None
    keywords: List[str] = field(default_factory=list)
    themes: List[str] = field(default_factory=list)
    geography: Optional[str] = None
    language: List[str] = field(default_factory=list)
    homepage: Optional[str] = None
    logo_url: Optional[str] = None
    created: Optional[str] = None
    updated: Optional[str] = None
    product_series: Optional[str] = None
    standards: List[str] = field(default_factory=list)
    product_version: Optional[str] = None
    version_notes: Optional[str] = None
    issues: Optional[str] = None
    content_sample: Optional[str] = None
    brand_slogan: Optional[str] = None
    use_cases: List[UseCase] = field(default_factory=list)
    recommended_data_products: List[str] = field(default_factory=list)
    output_file_formats: List[str] = field(default_factory=list)
```

### DataHolder

Data provider/holder information.

```python
@dataclass
class DataHolder:
    """Data holder/provider information."""
    
    # Required fields
    name: str
    email: str
    
    # Optional fields
    url: Optional[str] = None
    phone_number: Optional[str] = None
    address: Optional[str] = None
    business_identifiers: List[str] = field(default_factory=list)
    contact_person: Optional[str] = None
    contact_phone: Optional[str] = None
    contact_email: Optional[str] = None
    address_street: Optional[str] = None
    address_city: Optional[str] = None
    address_state: Optional[str] = None
    address_postal_code: Optional[str] = None
    address_country: Optional[str] = None
    ratings: Optional[Dict[str, Any]] = None
    organizational_description: Optional[str] = None
```

### DataContract

Data contract specifications.

```python
@dataclass
class DataContract:
    """Data contract specifications."""
    
    # ODPS v4.0 fields
    id: Optional[str] = None
    type: Optional[str] = None  # ODCS or DCS
    contract_version: Optional[str] = None
    contract_url: Optional[str] = None
    spec: Optional[Dict[str, Any]] = None
    ref: Optional[str] = field(default=None, metadata={'json_key': '$ref'})
```

### License

License terms and conditions.

```python
@dataclass
class License:
    """License terms and conditions."""
    
    # Required field
    scope_of_use: str
    
    # Core optional fields
    geographical_area: List[str] = field(default_factory=list)
    permanent: bool = True
    exclusive: bool = False
    right_to_sublicense: bool = False
    right_to_modify: bool = False
    valid_from: Optional[str] = None
    valid_until: Optional[str] = None
    license_grant: Optional[str] = None
    license_name: Optional[str] = None
    license_url: Optional[str] = None
    
    # Extended optional attributes
    scope_details: Optional[str] = None
    termination_conditions: Optional[str] = None
    governance_specifics: Optional[str] = None
    audit_terms: Optional[str] = None
    warranties: Optional[str] = None
    damages: Optional[str] = None
    confidentiality_clauses: Optional[str] = None
```

### DataAccess & DataAccessMethod

Data access methods and endpoints.

```python
@dataclass
class DataAccessMethod:
    """Individual data access method."""
    
    name: Optional[Union[str, Dict[str, str]]] = None
    description: Optional[Union[str, Dict[str, str]]] = None
    output_port_type: Optional[str] = None
    format: Optional[str] = None
    access_url: Optional[str] = None
    authentication_method: Optional[str] = None
    specs_url: Optional[str] = None
    documentation_url: Optional[str] = None
    specification: Optional[Dict[str, Any]] = None

@dataclass
class DataAccess:
    """Data access configuration."""
    
    default: DataAccessMethod
    additional_methods: Dict[str, DataAccessMethod] = field(default_factory=dict)
```

### PricingPlans & PricingPlan

Pricing information and plans.

```python
@dataclass
class PricingPlan:
    """Individual pricing plan."""
    
    # Required fields
    name: Union[str, Dict[str, str]]
    price_currency: str
    
    # Optional fields
    price: Optional[float] = None
    billing_duration: Optional[str] = None
    unit: Optional[str] = None
    max_transactions_per_second: Optional[int] = None
    max_transactions_per_month: Optional[int] = None
    min_price: Optional[float] = None
    max_price: Optional[float] = None
    value_added_tax: Optional[float] = None
    valid_from: Optional[str] = None
    valid_to: Optional[str] = None
    additional_pricing_details: Optional[str] = None
    quality_profile_reference: Optional[str] = None
    sla_profile_reference: Optional[str] = None
    access_profile_reference: Optional[str] = None

@dataclass
class PricingPlans:
    """Pricing plans container."""
    
    plans: List[PricingPlan] = field(default_factory=list)
```

---

## Validation Framework

### ODPSValidationFramework

Main validation coordinator.

```python
class ODPSValidationFramework:
    """Validation framework that orchestrates multiple validators."""
    
    def __init__(self) -> None:
        """Initialize with default validators."""
    
    def add_validator(self, validator: ValidationRule) -> None:
        """Add a custom validator to the framework."""
    
    def remove_validator(self, validator_class: type) -> None:
        """Remove all validators of the specified class."""
    
    def validate(self, odp: 'OpenDataProduct') -> List[str]:
        """Run all validation rules against an OpenDataProduct."""
```

### ValidationRule

Abstract base class for validation rules.

```python
class ValidationRule(ABC):
    """Abstract base class for validation rules."""
    
    @abstractmethod
    def validate(self, odp: 'OpenDataProduct') -> List[str]:
        """Validate an OpenDataProduct and return list of error messages."""
```

### Built-in Validators

The framework includes 13 built-in validator classes:

- `CoreFieldValidator`: Validates required core fields
- `ProductDetailsValidator`: Validates product details
- `DataHolderValidator`: Validates data holder information
- `LicenseValidator`: Validates license information
- `DataAccessValidator`: Validates data access methods
- `PricingValidator`: Validates pricing plans
- `DataContractValidator`: Validates data contracts
- `SLAValidator`: Validates SLA specifications
- `DataQualityValidator`: Validates data quality specifications
- `PaymentGatewayValidator`: Validates payment gateways
- `ExtensionValidator`: Validates extension fields
- `FieldLengthValidator`: Validates field length limits
- `StandardsComplianceValidator`: Validates international standards compliance

---

## Exception Hierarchy

### Base Exception

```python
class ODPSError(Exception):
    """Base exception class for all ODPS library errors."""
    
    def __init__(self, message: str, details: Optional[dict] = None):
        super().__init__(message)
        self.message = message
        self.details = details or {}
```

### Validation Exceptions

```python
class ODPSValidationError(ODPSError):
    """Raised when ODPS document validation fails."""
    
    def __init__(self, message: str, errors: Optional[List[str]] = None, 
                 field: Optional[str] = None):
        # Implementation details...
    
    @classmethod
    def from_errors(cls, errors: List[str]) -> 'ODPSValidationError':
        """Create validation error from list of error messages."""

class ODPSFieldValidationError(ODPSValidationError):
    """Raised when a specific field fails validation."""
    
    def __init__(self, field: str, value: Any, message: str):
        # Implementation details...
```

### Parsing Exceptions

```python
class ODPSJSONParsingError(ODPSParsingError):
    """Raised when JSON parsing specifically fails."""

class ODPSYAMLParsingError(ODPSParsingError):
    """Raised when YAML parsing specifically fails."""
```

### Component Exceptions

Component-specific exceptions for detailed error handling:

- `ODPSDataContractError`
- `ODPSSLAError`
- `ODPSDataQualityError`
- `ODPSLicenseError`
- `ODPSDataAccessError`
- `ODPSDataHolderError`
- `ODPSPricingError`
- `ODPSPaymentGatewayError`

### Utility Functions

```python
def create_field_error(field: str, value: Any, message: str) -> ODPSFieldValidationError:
    """Create a field-specific validation error."""

def create_component_error(component: str, message: str) -> ODPSComponentError:
    """Create a component-specific error."""
```

---

## Type Protocols

### Core Protocols

```python
class ValidatableProtocol(Protocol):
    """Protocol for objects that can be validated."""
    
    def validate(self) -> bool:
        """Validate the object and return True if valid."""

class SerializableProtocol(Protocol):
    """Protocol for objects that can be serialized."""
    
    def to_dict(self) -> Dict[str, Any]:
        """Convert object to dictionary representation."""
    
    def to_json(self, indent: int = 2) -> str:
        """Convert object to JSON string."""

class ODPSDocumentProtocol(ValidatableProtocol, SerializableProtocol, Protocol):
    """Protocol for complete ODPS documents."""
    
    schema: str
    version: str
    
    @property
    def is_valid(self) -> bool:
        """Check if document is valid without raising exceptions."""
```

### Component Protocols

Specific protocols for each ODPS component:

- `ProductDetailsProtocol`
- `DataHolderProtocol`
- `DataAccessProtocol`
- `LicenseProtocol`
- `PricingPlansProtocol`
- `DataContractProtocol`
- And more...

### Utility Functions

```python
def is_validatable(obj: Any) -> bool:
    """Check if an object implements the ValidatableProtocol."""

def is_serializable(obj: Any) -> bool:
    """Check if an object implements the SerializableProtocol."""

def validate_protocol_compliance(obj: Any, protocol_name: str) -> List[str]:
    """Validate that an object complies with a specific protocol."""
```

---

## Enumerations

### ProductStatus

```python
class ProductStatus(Enum):
    """Valid product status values according to ODPS v4.0."""
    
    ANNOUNCEMENT = "announcement"
    DRAFT = "draft"
    DEVELOPMENT = "development"
    TESTING = "testing"
    ACCEPTANCE = "acceptance"
    PRODUCTION = "production"
    SUNSET = "sunset"
    RETIRED = "retired"
```

### ProductVisibility

```python
class ProductVisibility(Enum):
    """Valid product visibility values."""
    
    PRIVATE = "private"
    INVITATION = "invitation"
    ORGANIZATION = "organisation"  # British spelling per spec
    DATASPACE = "dataspace"
    PUBLIC = "public"
```

### DataContractType

```python
class DataContractType(Enum):
    """Valid data contract types."""
    
    ODCS = "ODCS"  # Open Data Contract Specification
    DCS = "DCS"    # Data Contract Specification
```

---

## Utility Functions

### ODPSValidator

Utility class with individual validation methods:

```python
class ODPSValidator:
    """Utility class containing individual validation methods."""
    
    @staticmethod
    def validate_iso639_language_code(code: str) -> bool:
        """Validate ISO 639-1 language code."""
    
    @staticmethod
    def validate_iso3166_country_codes(codes: List[str]) -> bool:
        """Validate ISO 3166-1 alpha-2 country codes."""
    
    @staticmethod
    def validate_currency_code(code: str) -> bool:
        """Validate ISO 4217 currency code."""
    
    @staticmethod
    def validate_email(email: str) -> bool:
        """Validate email address according to RFC 5322."""
    
    @staticmethod
    def validate_phone_number(phone: str) -> bool:
        """Validate phone number according to ITU-T E.164."""
    
    @staticmethod
    def validate_iso8601_date(date_str: str) -> bool:
        """Validate ISO 8601 date format."""
    
    @staticmethod
    def validate_uri(uri: str) -> bool:
        """Validate URI according to RFC 3986."""
    
    # Additional validation methods...
```

---

## Performance Features

### Caching System

The library implements intelligent caching for optimal performance:

- **Validation Caching**: Results cached based on object state hash
- **Serialization Caching**: JSON/YAML output cached with invalidation
- **Hash-based Invalidation**: Automatic cache clearing on state changes

### Memory Optimization

- **__slots__**: Used in core classes for memory efficiency
- **Lazy Loading**: Components loaded only when accessed
- **Efficient Data Structures**: Optimized internal representations

---

## Usage Examples

See the [examples directory](../examples/) for comprehensive usage examples:

- `basic_usage.py`: Fundamental operations and features
- `advanced_features.py`: Advanced functionality and customization

For more information, see the [README](../README.md) and [CHANGELOG](../CHANGELOG.md).