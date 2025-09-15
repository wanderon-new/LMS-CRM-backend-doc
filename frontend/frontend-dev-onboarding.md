# Frontend Development Onboarding Guide

## 📋 Prerequisites

Before starting, ensure you have the following installed on your system:

### Required Software
- **Node.js**: Version 18.17+ or 20+
  ```bash
  node --version  # Should be 18.17+ or 20+
  npm --version   # Should be 9+
  ```
- **Git**: Latest version
- **VS Code** (recommended) with extensions:
  - ES7+ React/Redux/React-Native snippets
  - TypeScript Importer
  - Auto Rename Tag
  - Prettier - Code formatter
  - ESLint

### Development Tools
- **Postman** or **Insomnia** for API testing
- **React Developer Tools** browser extension
- **Redux DevTools** browser extension

## 🚀 Getting Started

### 1. Clone and Setup Frontend Project

```bash
# Navigate to your projects directory
cd /home/omp/projects/node/lead-processor-frontend

# Install dependencies
npm install

# Create environment files
cp .env.example .env.local
cp .env.example .env.development
```

### 2. Environment Configuration

Create and configure your `.env.local` file:

```env
# API Configuration
NEXT_PUBLIC_API_BASE_URL=http://localhost:3000
NEXT_PUBLIC_API_TIMEOUT=30000

# Authentication
NEXT_PUBLIC_SESSION_TIMEOUT=3600000

# Feature Flags
NEXT_PUBLIC_ENABLE_DEV_TOOLS=true
NEXT_PUBLIC_ENABLE_LOGGING=true

# Environment
NODE_ENV=development
NEXT_PUBLIC_ENV=development
```

### 3. Start Development Server

```bash
# Start the frontend development server
npm run dev

# The application will be available at:
# http://localhost:3001
```

### 4. Backend Setup (Required)

Ensure the backend is running for full functionality:

```bash
# In a separate terminal, navigate to backend
cd /home/omp/projects/node/lead-processor

# Start backend server
npm run dev

# Backend will be available at:
# http://localhost:3000
```

## 🏗️ Project Structure Understanding

### Key Directories

```
lead-processor-frontend/
├── app/                        # Next.js App Router structure
│   ├── (routes)/              # Route groups
│   ├── _components/           # Reusable components
│   ├── _redux/               # State management
│   ├── _styles/              # Global styles
│   ├── _theme/               # Material-UI theming
│   ├── _types/               # TypeScript definitions
│   └── _utils/               # Utility functions
├── public/                    # Static assets
├── docs/                      # Project documentation
└── package.json              # Dependencies and scripts
```

### Important Files

- **`app/layout.tsx`**: Root layout with providers
- **`app/middleware.ts`**: Route protection and authentication
- **`app/_redux/store.ts`**: Redux store configuration
- **`app/_theme/theme.ts`**: Material-UI theme configuration
- **`next.config.mjs`**: Next.js configuration

## 🔧 Development Workflow

### 1. Creating New Features

#### Step 1: Create Feature Branch
```bash
git checkout -b feature/new-feature-name
```

#### Step 2: Create Components
```bash
# Create component directory
mkdir -p app/_components/NewFeature

# Create component files
touch app/_components/NewFeature/NewFeatureComponent.tsx
touch app/_components/NewFeature/index.ts
```

#### Step 3: Component Template
```typescript
// app/_components/NewFeature/NewFeatureComponent.tsx
'use client';

import { Box, Typography } from '@mui/material';
import { useState, useEffect } from 'react';

interface NewFeatureProps {
  // Define props here
}

export default function NewFeatureComponent({ }: NewFeatureProps) {
  const [loading, setLoading] = useState(false);
  
  useEffect(() => {
    // Component initialization
  }, []);
  
  return (
    <Box>
      <Typography variant="h4">New Feature</Typography>
      {/* Component content */}
    </Box>
  );
}
```

#### Step 4: Create Redux Slice (if needed)
```typescript
// app/_redux/slices/newFeatureSlice.ts
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

interface NewFeatureState {
  data: any[];
  loading: boolean;
  error: string | null;
}

const initialState: NewFeatureState = {
  data: [],
  loading: false,
  error: null,
};

export const fetchNewFeatureData = createAsyncThunk(
  'newFeature/fetchData',
  async () => {
    // API call here
  }
);

const newFeatureSlice = createSlice({
  name: 'newFeature',
  initialState,
  reducers: {
    // Synchronous actions
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchNewFeatureData.pending, (state) => {
        state.loading = true;
      })
      .addCase(fetchNewFeatureData.fulfilled, (state, action) => {
        state.loading = false;
        state.data = action.payload;
      })
      .addCase(fetchNewFeatureData.rejected, (state, action) => {
        state.loading = false;
        state.error = action.error.message || 'An error occurred';
      });
  },
});

export default newFeatureSlice.reducer;
```

### 2. Working with APIs

#### Create Data Communication Module
```typescript
// app/lib/dataCom/newFeature.dataCom.ts
import { httpService } from '../httpService';

export interface NewFeatureData {
  id: string;
  name: string;
  // Define data structure
}

export const getNewFeatureData = async (): Promise<NewFeatureData[]> => {
  const response = await httpService.get('/api/new-feature');
  return response.data;
};

export const createNewFeatureItem = async (data: Partial<NewFeatureData>): Promise<NewFeatureData> => {
  const response = await httpService.post('/api/new-feature', data);
  return response.data;
};

export const updateNewFeatureItem = async (id: string, data: Partial<NewFeatureData>): Promise<NewFeatureData> => {
  const response = await httpService.patch(`/api/new-feature/${id}`, data);
  return response.data;
};
```

### 3. Adding New Routes

#### Create Page Component
```typescript
// app/(routes)/new-feature/page.tsx
'use client';

import { Box } from '@mui/material';
import NewFeatureComponent from '../../_components/NewFeature/NewFeatureComponent';

export default function NewFeaturePage() {
  return (
    <Box>
      <NewFeatureComponent />
    </Box>
  );
}
```

#### Update Navigation (if needed)
```typescript
// app/_components/Navigation/LeftNav.tsx
// Add new menu item to navigation
```

## 🎨 Styling Guidelines

### Material-UI Usage

```typescript
// Use sx prop for styling
<Box
  sx={{
    padding: 2,
    marginBottom: 3,
    backgroundColor: 'background.paper',
    borderRadius: 1,
    boxShadow: 1,
  }}
>
  Content
</Box>

// Use theme values
const theme = useTheme();
<Typography
  sx={{
    color: theme.palette.primary.main,
    fontSize: theme.typography.h6.fontSize,
  }}
>
  Themed Text
</Typography>
```

### Responsive Design

```typescript
// Use breakpoints for responsive design
<Box
  sx={{
    display: { xs: 'block', md: 'flex' },
    padding: { xs: 1, sm: 2, md: 3 },
    width: { xs: '100%', md: '50%' },
  }}
>
  Responsive Content
</Box>
```

## 🔍 Testing Guidelines

### Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage
npm run test:coverage
```

### Writing Component Tests

```typescript
// __tests__/NewFeatureComponent.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Provider } from 'react-redux';
import { store } from '../app/_redux/store';
import NewFeatureComponent from '../app/_components/NewFeature/NewFeatureComponent';

const renderWithProvider = (component: React.ReactElement) => {
  return render(
    <Provider store={store}>
      {component}
    </Provider>
  );
};

describe('NewFeatureComponent', () => {
  it('renders correctly', () => {
    renderWithProvider(<NewFeatureComponent />);
    expect(screen.getByText('New Feature')).toBeInTheDocument();
  });

  it('handles user interaction', () => {
    renderWithProvider(<NewFeatureComponent />);
    const button = screen.getByRole('button');
    fireEvent.click(button);
    // Assert expected behavior
  });
});
```

## 🐛 Debugging Guide

### Development Tools

1. **React Developer Tools**
   - Inspect component hierarchy
   - View props and state
   - Profile component performance

2. **Redux DevTools**
   - Monitor state changes
   - Time-travel debugging
   - Action replay

3. **Browser DevTools**
   - Network tab for API calls
   - Console for logging
   - Application tab for storage

### Common Debugging Techniques

```typescript
// Add logging for debugging
useEffect(() => {
  console.log('Component mounted, props:', props);
}, []);

// Debug API calls
try {
  const response = await apiCall();
  console.log('API Response:', response);
} catch (error) {
  console.error('API Error:', error);
}

// Debug Redux state
const someValue = useSelector(selectSomeValue);
console.log('Redux state value:', someValue);
```

## 📝 Code Standards

### TypeScript Guidelines

```typescript
// Always define interfaces for props
interface ComponentProps {
  title: string;
  items: Item[];
  onItemClick?: (item: Item) => void;
}

// Use proper typing for event handlers
const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
  // Handle click
};

// Type API responses
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}
```

### Component Guidelines

```typescript
// Functional components with proper typing
const MyComponent: React.FC<ComponentProps> = ({ title, items, onItemClick }) => {
  // Use meaningful variable names
  const [isModalOpen, setIsModalOpen] = useState(false);
  
  // Group related logic
  const handleModalOpen = useCallback(() => {
    setIsModalOpen(true);
  }, []);
  
  // Early returns for conditional rendering
  if (!items.length) {
    return <EmptyState />;
  }
  
  return (
    <Box>
      {/* JSX content */}
    </Box>
  );
};
```

## 🔄 State Management Best Practices

### Redux Usage

```typescript
// Use createAsyncThunk for API calls
export const fetchData = createAsyncThunk(
  'feature/fetchData',
  async (params: FetchParams, { rejectWithValue }) => {
    try {
      const response = await apiCall(params);
      return response.data;
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);

// Use selectors for computed values
export const selectFilteredItems = createSelector(
  [selectItems, selectFilters],
  (items, filters) => {
    return items.filter(item => 
      filters.category ? item.category === filters.category : true
    );
  }
);
```

## 🚀 Deployment Preparation

### Build Process

```bash
# Create production build
npm run build

# Test production build locally
npm start

# Run type checking
npm run type-check

# Run linting
npm run lint

# Run tests
npm test
```

### Pre-deployment Checklist

- [ ] All tests passing
- [ ] No TypeScript errors
- [ ] No linting errors
- [ ] Environment variables configured
- [ ] API endpoints updated for production
- [ ] Performance optimizations applied
- [ ] Accessibility testing completed

## 📚 Useful Resources

### Documentation
- [Next.js Documentation](https://nextjs.org/docs)
- [Material-UI Documentation](https://mui.com/material-ui/getting-started/overview/)
- [Redux Toolkit Documentation](https://redux-toolkit.js.org/)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/)

### Development Tools
- [React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- [Redux DevTools](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd)

## 🆘 Getting Help

### Internal Resources
- Check existing documentation in `/docs`
- Review similar components for patterns
- Ask team members for guidance

### External Resources
- Stack Overflow for general questions
- GitHub issues for library-specific problems
- Official documentation for authoritative answers

### Troubleshooting Common Issues

1. **Module not found errors**
   ```bash
   npm install
   npm run build
   ```

2. **TypeScript compilation errors**
   ```bash
   npm run type-check
   ```

3. **Styling issues**
   - Check Material-UI theme configuration
   - Verify responsive breakpoints
   - Test in different screen sizes

4. **State management issues**
   - Use Redux DevTools to inspect state
   - Verify action dispatching
   - Check selector implementations

This guide should help you get started with frontend development. Remember to follow the established patterns and ask for help when needed!
