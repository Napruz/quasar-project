.card-header-bg-image {
  height: 360px;
  background: radial-gradient(
      68% 100% at 100% 0%,
      rgb(119, 41, 255),
      rgba(116, 42, 245, 0)
    ),
    radial-gradient(
      100% 98% at 100% 100%,
      rgb(4, 165, 236),
      rgba(41, 102, 227, 0)
    ),
    linear-gradient(0deg, rgb(51, 90, 228), rgb(16, 25, 131));
}

.event-state {
  position: relative;
  left: 10px;
  top: 5px;
  display: flex;
  gap: 5px; /* Расстояние между плашками */
}

.event-state-item {
  width: 77px;
  height: 20px;
  background-color: #e0e0e0; /* Цвет фона */
  color: #333; /* Цвет текста */
  font-size: 12px;
  font-weight: bold;
  text-align: center;
  line-height: 20px; /* Выравнивание текста по центру */
  border-radius: 4px; /* Сглаживание углов */
}

