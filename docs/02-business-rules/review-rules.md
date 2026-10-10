# Review and Rating Business Rules

## 1. Verified Purchase Invariant

1. **Eligibility Enforcement**:
   - Only customers who have purchased the specific product and whose order has reached `DELIVERED` status are permitted to submit a review.
   - Anonymous visitors or customers with pending/cancelled orders are strictly blocked from posting reviews.
2. **One Review Per Product Per Order**:
   - A customer may submit at most one review per product per verified purchase order.
   - Customers may edit their previously submitted review at any time.

## 2. Review Structure & Moderation

1. **Rating Scale**:
   - Ratings are integer values strictly between 1 and 5 stars ($1 \le \text{rating} \le 5$).
2. **User-Generated Content**:
   - Reviews may include a title (max 100 characters), body text (max 2,000 characters), and up to 5 verified customer photos/videos.
3. **Automated & Manual Moderation**:
   - Reviews pass through an automated profanity and spam filter.
   - Clean reviews with 4 or 5 stars from verified buyers auto-publish immediately.
   - Reviews with 1 or 2 stars or flagged content enter an internal administrative moderation queue (`PENDING_APPROVAL`) for merchant review. Merchant disapproval requires documenting a valid policy reason (e.g., hate speech, competitor spam).

## 3. Aggregate Rating Recalculation

1. **Asynchronous Recalculation**:
   - When a review is published, updated, or removed, the Review Service emits a `ReviewPublished` event.
   - A Kafka consumer recalculates the product's average star rating and total review count, updating both PostgreSQL and OpenSearch catalog documents.
