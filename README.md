# Travel App Demo

A Vue.js travel booking application demo showcasing hotel search results and hotel detail pages.

## 🌟 Features

- **Hotel Search Results** - Grid view with advanced filtering and sorting
- **Hotel Detail Page** - Comprehensive hotel information with flight options
- **Alternative Flights** - Browse and select different flight combinations
- **Price Calendar** - Interactive calendar for date and price selection
- **Responsive Design** - Mobile-friendly interface
- **Advanced Filters** - Star rating, price range, amenities, and more

## 🚀 Quick Start

### Prerequisites

- Node.js (v16 or higher)
- npm or yarn

### Installation

1. **Clone the repository**

   ```bash
   git clone <repository-url>
   cd travel-app-demo
   ```

2. **Install dependencies**

   ```bash
   npm install
   # or
   yarn install
   ```

3. **Run the development server**

   ```bash
   npm run dev
   # or
   yarn dev
   ```

4. **Open your browser**
   ```
   http://localhost:3000
   ```

## 📱 Demo Pages

### 1. Search Results Page (`/`)

- Browse available hotels in Dubai
- Filter by price, star rating, amenities
- Sort by price, popularity, rating
- Pagination for large result sets

### 2. Hotel Detail Page (`/hotel-detail`)

- Detailed hotel information
- Interactive price calendar
- Flight + Hotel packages
- Alternative flight options
- Booking form integration

## 🗂️ Project Structure

```
├── components/
│   ├── AlternativeFlights.vue    # Flight selection component
│   ├── EnquiryModal.vue         # Contact/booking modal
│   ├── Footer.vue               # Site footer
│   ├── Header.vue               # Site header with navigation
│   ├── HotelCalendar.vue        # Price & availability calendar
│   ├── HotelCard.vue            # Hotel listing card
│   ├── HotelDetailHeader.vue    # Hotel detail header
│   ├── HotelList.vue            # Hotel grid with pagination
│   ├── HotelOfferCard.vue       # Flight + hotel package card
│   ├── HotelRefineForm.vue      # Search refinement form
│   ├── RecentlyViewed.vue       # Recently viewed hotels
│   ├── Search.vue               # Main search container
│   ├── SearchFilters.vue        # Advanced filtering sidebar
│   └── SearchForm.vue           # Search input form
├── pages/
│   ├── index.vue                # Search results page
│   └── hotel-detail.vue         # Hotel detail page
└── static/data/
    ├── flights.json             # Sample flight data
    ├── hotels.json              # Sample hotel data
    └── menu.json                # Navigation menu data
```

## 🎨 Key Components

- **HotelCard** - Displays hotel information with pricing and ratings
- **SearchFilters** - Advanced filtering with price sliders and checkboxes
- **AlternativeFlights** - Flight comparison and selection interface
- **HotelCalendar** - Interactive calendar for date and price selection
- **Pagination** - Handles large datasets with smooth navigation

## 🔧 Technologies Used

- **Vue.js 3** - Progressive JavaScript framework
- **Nuxt.js** - Vue.js framework for production
- **CSS Grid & Flexbox** - Modern responsive layouts
- **JSON Data** - Static data for demo purposes

## 📋 Sample Data

The demo includes realistic sample data for:

- 15 luxury hotels in Dubai
- 10 different flight combinations
- Pricing and availability information
- Hotel amenities and ratings

## 🚧 Demo Limitations

This is a frontend demo with:

- Static JSON data (no backend integration)
- Simulated booking flows
- No payment processing
- No user authentication

## 🔮 Future Enhancements

- Backend API integration
- Real payment processing
- User accounts and saved searches
- Advanced search with maps
- Review and rating system
- Multi-language support

## 📄 License

This project is for demonstration purposes only.

---

**Note**: This is a demo application showcasing modern travel booking UI/UX patterns. All hotel and flight data is fictional and for demonstration purposes only.
